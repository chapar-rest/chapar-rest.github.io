---
title: "Examples"
weight: 2
summary: "Ready-to-use scripts: tokens, signatures, request changes, tests, gRPC and GraphQL"
---

Copy these into the **Before** (pre-request) or **After** (post-request) section of a request's **Actions** tab, with **Python** selected. Examples marked *mock server* work as they are against the free [Chapar mock server](../../mockserver) with an environment that has `baseUrl` set to `https://mocks.chapar.rest/api/v1`.

## Authentication

### Save the token from a login response

*After*, on your login request:

```python
@chapar.test("login succeeded")
def _():
    assert response.status_code == 200, response.text

data = response.json()
chapar.env.set("token", data["access_token"])
chapar.env.set("refreshToken", data.get("refresh_token", ""))
chapar.log("logged in, token expires in", data.get("expires_in"), "seconds")
```

Other requests then use **Auth** › **Bearer** with the token `{{token}}`, or set it on the collection once and **Inherit** it.

### Log in automatically when the token is missing or expired

*Before*, on any request that needs a token. The script fetches a token with the `requests` package and remembers when it expires:

```python
import time
import requests

if time.time() > float(chapar.env.get("tokenExpiresAt", "0")):
    r = requests.post(
        chapar.env.get("authUrl"),
        data={
            "grant_type": "client_credentials",
            "client_id": chapar.env.get("clientId"),
            "client_secret": chapar.env.get("clientSecret"),
        },
        timeout=5,
    )
    r.raise_for_status()
    token = r.json()
    chapar.env.set("token", token["access_token"])
    chapar.env.set("tokenExpiresAt", time.time() + token["expires_in"] - 30)
    chapar.log("fetched a new token")

request.headers["Authorization"] = "Bearer " + chapar.env.get("token")
```

{{< callout type="info" >}}
Scripts run inside a Docker container, so `localhost` in a script means the container, not your machine. To reach a server on your machine, use `host.docker.internal` (Docker Desktop and OrbStack).
{{< /callout >}}

### Skip the request until you are logged in

*Before*:

```python
if not chapar.env.get("token"):
    chapar.skip("no token yet: send the Login request first")
```

### Forget a token the server rejects

*After*:

```python
if response.status_code == 401:
    chapar.env.unset("token")
    chapar.log("token rejected; it will be fetched again on the next send")
```

## Changing the request

### Sign the body with HMAC *(mock server)*

*Before*, on a `POST {{baseUrl}}/echo` with a JSON body. `request.resolved.body` is the body with `{{variables}}` filled in, exactly as it will be sent:

```python
import hashlib, hmac, time

key = chapar.env.get("apiToken", "").encode()
if not key:
    chapar.skip("set apiToken in the environment first")

body = request.resolved.body.encode()
request.headers["X-Timestamp"] = str(int(time.time()))
request.headers["X-Signature"] = hmac.new(key, body, hashlib.sha256).hexdigest()
```

*After*, check that the server received it (the mock server's `/echo` returns the headers it got):

```python
echo = response.json()

@chapar.test("server received the signature")
def _():
    assert "X-Signature" in echo["headers"]
```

![The pre-request step in the timeline](../images/pre-request-timeline.png)

### Add a request id and an idempotency key

*Before*:

```python
import uuid

request.headers["X-Request-Id"] = str(uuid.uuid4())
if request.method in ("POST", "PUT", "PATCH"):
    if not request.headers.get("Idempotency-Key"):
        request.headers["Idempotency-Key"] = str(uuid.uuid4())
```

### Edit the JSON body *(mock server)*

*Before*, on `POST {{baseUrl}}/todos`:

```python
todo = request.json()
todo["title"] = todo["title"].strip()
todo.setdefault("tags", []).append("from-chapar")
todo["due_at"] = "{{timeNow}}"          # filled in after the script
request.set_json(todo)
```

### Switch the target by environment

*Before*:

```python
if chapar.env.name == "Production" and request.method != "GET":
    chapar.skip("write requests are blocked in Production")

request.query["debug"] = "1" if chapar.env.name == "Local" else "0"
```

## Testing responses

### Status, time, headers and body *(mock server)*

*After*, on `GET {{baseUrl}}/todos`:

```python
todos = response.json()["data"]

@chapar.test("status is 200")
def _():
    assert response.status_code == 200, response.status

@chapar.test("responds in under a second")
def _():
    assert response.elapsed_ms < 1000, f"took {response.elapsed_ms:.0f} ms"

@chapar.test("returns JSON")
def _():
    assert response.headers.get("Content-Type", "").startswith("application/json")

@chapar.test("every todo has an id and a title")
def _():
    for t in todos:
        assert t.get("id") and t.get("title"), t

@chapar.test("priorities are valid")
def _():
    assert {t["priority"] for t in todos} <= {"low", "medium", "high"}
```

![Test results in the timeline](../images/post-request-tests.png)

### Keep values for the next request *(mock server)*

*After*, on `POST {{baseUrl}}/todos`:

```python
if response.status_code == 201:
    todo = response.json()["data"]
    chapar.env.set("todoId", todo["id"])
    chapar.env.set("todoTags", todo["tags"])     # a list: stored as JSON text
```

Then use `{{baseUrl}}/todos/{{todoId}}` in the next request. In another script, read the list back with `json.loads(chapar.env.get("todoTags"))`.

### Walk through pages

*After*, on a paged list request whose URL ends in `?offset={{offset}}&limit=20`:

```python
page = response.json()
offset = page["offset"] + page["limit"]
if offset < page["total"]:
    chapar.env.set("offset", offset)
    chapar.log("next page starts at", offset)
else:
    chapar.env.set("offset", 0)
    chapar.log("that was the last page")
```

### Read a cookie *(mock server)*

*After*, on `GET {{baseUrl}}/cookies/set?session=abc`:

```python
session = response.cookie("session")
chapar.test("server set a session cookie", lambda: session == "abc")
chapar.env.set("sessionCookie", session)
```

## gRPC

For gRPC, `request.url` is the server address, `request.method` the full method name, `request.metadata` the metadata, and `request.body` the JSON message. `response.status_code` is the gRPC status code, where `0` is OK.

### Add metadata and fill the message *(mock server)*

*Before*, on `mock.v1.TodoService/CreateTodo`:

```python
import uuid

request.metadata["x-session-id"] = chapar.env.get("sessionId", "public")
request.metadata["x-request-id"] = str(uuid.uuid4())

msg = request.json()
msg.setdefault("priority", "TODO_PRIORITY_MEDIUM")
request.set_json(msg)
```

### Check the status, trailers and message *(mock server)*

*After*, on `mock.v1.TodoService/ListTodos`:

```python
todos = response.json().get("todos", [])

@chapar.test("gRPC status is OK")
def _():
    assert response.status_code == 0, f"{response.status}: {response.error}"

chapar.log(len(todos), "todos, trailers:", response.trailers.to_dict())
if todos:
    chapar.env.set("todoId", todos[0]["id"])
```

![A gRPC post-request script](../images/grpc-script.png)

### Expect an error status *(mock server)*

*After*, on `mock.v1.UtilityService/Status` with the message `{"code": 5, "message": "gone"}`:

```python
@chapar.test("returns NOT_FOUND")
def _():
    assert response.status_code == 5
    assert not response.ok
```

## GraphQL

For GraphQL, the document and variables are in `request.graphql`, and a response's `data` and `errors` are parsed for you.

### Set query variables

*Before*:

```python
variables = request.graphql.variables or {}
variables["code"] = chapar.env.get("countryCode", "IR")
request.graphql.variables = variables
```

### Check for errors and keep data

*After*, on a `country(code: $code)` query against `https://countries.trevorblades.com/graphql`:

```python
@chapar.test("no GraphQL errors")
def _():
    assert not response.errors, response.errors

country = response.data["country"]
chapar.env.set("capital", country["capital"])
chapar.log(country["name"], "-", country["capital"])
```

## Debugging scripts

- Use `print(...)` or `chapar.log(...)` freely; the output is in the **Timeline**, under the **Pre-request** or **Post-request** step.
- `print(request.resolved.url)` and `print(request.resolved.headers.to_dict())` show what will really be sent.
- An exception names its line: `line 4: KeyError: 'data'`. Use `response.json().get("data")` when a field is optional.
- The **Console** in the footer has Chapar's own logs, including executor errors.
