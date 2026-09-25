---
title: "API Reference"
weight: 1
summary: "Everything a script can use: request, response, the chapar module, environment, tests and logging"
---

Three names are defined in every script, with no imports needed:

| Name | What it is |
|------|------------|
| `request` | The request being sent. A pre-request script may change it. |
| `response` | The response, in post-request scripts. `None` in pre-request scripts. |
| `chapar` | The environment, tests, logging and skipping. `import chapar` works too. |

The Python standard library is available (`json`, `hashlib`, `hmac`, `base64`, `datetime`, `uuid`, `re`, …), as is the [`requests`](https://requests.readthedocs.io/) package.

## `request`

| Attribute | HTTP | gRPC | GraphQL |
|-----------|------|------|---------|
| `request.protocol` | `"http"` | `"grpc"` | `"graphql"` |
| `request.url` | The URL | The server address, `host:port` | The endpoint URL |
| `request.method` | `GET`, `POST`, … | The full method, `package.Service/Method` | `POST` |
| `request.headers` | Headers, as [`Pairs`](#pairs) | The metadata, as `Pairs` (also `request.metadata`) | Headers |
| `request.body` | The body, as text | The JSON message, as text | — |
| `request.query` | Query params, as `Pairs` (case-sensitive) | — | — |
| `request.path_params` | The `{name}` path params, as a `dict` | — | — |
| `request.graphql` | `None` | `None` | `.query` (the document) and `.variables` (a `dict` when valid JSON) |
| `request.resolved` | The same request with `{{variables}}` filled in, read-only | | |

| Method | Does |
|--------|------|
| `request.json()` | Parses the body as JSON. For GraphQL, returns `{"query": …, "variables": …}`. |
| `request.set_json(obj)` | Replaces the body with `obj` encoded as JSON (HTTP and gRPC). |

The fields keep their `{{variables}}`: Chapar fills them in **after** the pre-request script runs, so a value you set can itself contain `{{variables}}`. Read `request.resolved` (`.url`, `.headers`, `.body`, `.query`, `.graphql`, `.json()`) when you need the final values, for example to sign exactly what will be sent.

In a **pre-request** script, every change to `url`, `method`, `headers`, `body`, `query`, `path_params` and `graphql` is applied to the request that is sent. The saved request doesn't change. In a **post-request** script, `request` is read-only information about what was sent.

```python
request.headers["X-Request-Id"] = chapar.env.get("requestId", "")
request.query["page"] = "2"
body = request.json()
body["sentAt"] = "{{timeNow}}"        # filled in after the script
request.set_json(body)
```

## `response`

Available in post-request scripts.

| Attribute | Meaning |
|-----------|---------|
| `response.protocol` | `"http"`, `"grpc"` or `"graphql"` |
| `response.status_code` | The HTTP status code. For gRPC, the gRPC status code (`0` is OK, `5` is NOT_FOUND, …). |
| `response.status` | The status text, for example `"200 OK"`, or the gRPC code name, for example `"OK"`. |
| `response.ok` | `True` for a 2xx status, or gRPC code 0, when the request didn't fail. |
| `response.headers` | The response headers, as [`Pairs`](#pairs). |
| `response.text`, `response.body` | The body as text. For gRPC, the JSON response message. |
| `response.elapsed_ms` | How long the request took, in milliseconds. |
| `response.size` | The body size in bytes. |
| `response.error` | Why the request failed, or `None`. |
| `response.cookies` | The cookies the response set: a list of objects with `name`, `value`, `domain`, `path`, `expires`, `secure` and `http_only`. |
| `response.metadata` | gRPC: the response metadata (headers), as `Pairs`. |
| `response.trailers` | gRPC: the trailers, as `Pairs`. |
| `response.data` | GraphQL: the `data` field of the body. |
| `response.errors` | GraphQL: the `errors` field of the body, or `None`. |

| Method | Does |
|--------|------|
| `response.json()` | Parses the body as JSON. Raises `ValueError` with a clear message when the body is empty or not JSON. |
| `response.cookie(name, default=None)` | The value of the cookie `name` that the response set. |

## `chapar`

### Environment

`chapar.env` is the active environment.

| Call | Does |
|------|------|
| `chapar.env.get(key, default=None)` | The value of a variable, as a string, or `default`. |
| `chapar.env.set(key, value)` | Sets a variable. Strings are stored as they are; numbers, booleans, lists and dicts are stored as JSON text. |
| `chapar.env.unset(key)` | Removes a variable. |
| `chapar.env.all()` | Every variable, as a `dict`. |
| `chapar.env.name` | The environment's name. |
| `chapar.env[key]`, `chapar.env[key] = v`, `del chapar.env[key]`, `key in chapar.env` | Dict-style access. |
| `chapar.get_env(k, d)`, `chapar.set_env(k, v)`, `chapar.unset_env(k)` | Shorthands. |

Changes are saved to the environment after the script finishes, in pre-request and post-request scripts alike. The request that is being sent already sees values a pre-request script set.

{{< callout type="info" >}}
Select an environment before sending. With **No Environment**, there is nowhere to save values, and `chapar.env.set` has no lasting effect.
{{< /callout >}}

### Tests

`chapar.test(name, fn)` runs `fn` and records whether it passed. A test fails when `fn` raises, typically through `assert`. It returns `True` or `False`. Use it as a decorator to write the test inline:

```python
@chapar.test("status is 201")
def _():
    assert response.status_code == 201, response.status

@chapar.test("returns the new id")
def _():
    assert response.json()["data"]["id"]

chapar.test("is fast", lambda: response.elapsed_ms < 500)
```

A failing test doesn't stop the script. Results appear in the timeline as `✓ name` or `✗ name: reason`, and the step is marked failed when any test fails.

### Logging

`chapar.log(*args, sep=" ")` and `print(...)` write to the script's output, shown in the timeline. Output is capped at 256 KB per run.

### Skipping a request

`chapar.skip(reason="")`, in a pre-request script, stops the script and cancels the send. The response pane shows *the pre-request script skipped this request: reason*. Calling it in a post-request script raises an error.

```python
if not chapar.env.get("token"):
    chapar.skip("log in first: send the Login request")
```

### Context

| Name | Value |
|------|-------|
| `chapar.phase` | `"pre"` or `"post"` |
| `chapar.protocol` | `"http"`, `"grpc"` or `"graphql"` |
| `chapar.request`, `chapar.response` | The same objects as `request` and `response` |
| `chapar.on_response` | In a post-request script, set it to a function; it is called with `response` after the script's body runs. |

## `Pairs`

Headers, gRPC metadata and trailers, and query params are `Pairs`: ordered key/value lists that read like a `dict` but can hold a key more than once. Header and metadata keys match case-insensitively; query keys are case-sensitive.

| Call | Does |
|------|------|
| `p[key]` | The first value; `KeyError` if missing. |
| `p.get(key, default=None)` | The first value, or `default`. |
| `p.get_all(key)` | Every value of `key`, as a list. |
| `p[key] = value`, `p.set(key, value)` | Replaces every value of `key` with one value. |
| `p.add(key, value)` | Appends another value for `key`. |
| `del p[key]`, `p.remove(key)` | Removes every value of `key` (`remove` ignores a missing key). |
| `key in p`, `len(p)`, `for key in p` | Membership, count and iteration over the keys. |
| `p.keys()`, `p.values()`, `p.items()`, `p.pairs()` | Lists; `pairs()` includes duplicates in order. |
| `p.to_dict()` | A plain `dict`. |

```python
request.headers.add("Accept", "application/json")
request.headers.add("Accept", "text/plain")
request.headers.get_all("accept")      # ['application/json', 'text/plain']
request.headers.remove("X-Debug")
```

## Limits

| Limit | Value |
|-------|-------|
| Run time per script | 10 seconds |
| Output per run | 256 KB |
| Memory for the executor | 512 MB |
| Writable disk | `/tmp`, 64 MB |
| Python version | 3.11 |

Each run happens in a new process: variables, imports and files from one run are gone in the next. Keep state in the environment.
