---
title: "REST API"
weight: 1
summary: "Todo and utility endpoints of the mock server over HTTP"
---

Base URL: `https://mocks.chapar.rest/api/v1`. All requests and responses are JSON unless noted otherwise. Errors always look like this:

```json
{ "error": "invalid argument: title is required" }
```

The full contract is in the [OpenAPI spec](https://github.com/chapar-rest/mock-server/blob/main/api/rest/openapi.yaml), which you can import into Chapar.

## Todos

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/todos` | List todos. Filters: `completed`, `priority`, `q` (text search), `limit` (1-100, default 20), `offset` |
| `POST` | `/todos` | Create a todo. Returns `201` |
| `GET` | `/todos/{id}` | Get a todo |
| `PUT` | `/todos/{id}` | Replace a todo; omitted fields are reset to their defaults |
| `PATCH` | `/todos/{id}` | Update only the fields you send. `"due_at": null` clears the due date |
| `DELETE` | `/todos/{id}` | Delete a todo. Returns `204` |
| `POST` | `/todos/reset` | Restore the session's sample todos |

All todo endpoints accept the optional `X-Session-Id` header. See [Sessions](../#sessions).

A todo looks like this:

```json
{
  "id": "0252e97f-54e3-4a1c-a514-77b63b2456c6",
  "title": "Try Chapar",
  "description": "Send a request to the mock server from Chapar.",
  "completed": false,
  "priority": "high",
  "tags": ["chapar", "getting-started"],
  "due_at": "2030-01-01T09:00:00Z",
  "created_at": "2026-09-22T19:33:58.752091Z",
  "updated_at": "2026-09-22T19:33:58.752091Z"
}
```

| Field | Rules |
|-------|-------|
| `title` | Required, 1-200 characters |
| `description` | Up to 2000 characters |
| `priority` | `low`, `medium` or `high`; defaults to `medium` |
| `tags` | Up to 10 tags of 1-32 characters |
| `due_at` | RFC 3339 date-time, or `null` |

### Examples

Create a todo:

```bash
curl -X POST https://mocks.chapar.rest/api/v1/todos \
  -H 'X-Session-Id: alice' \
  -H 'Content-Type: application/json' \
  -d '{"title": "Buy milk", "priority": "high", "tags": ["home"]}'
```

List your unfinished high-priority todos:

```bash
curl 'https://mocks.chapar.rest/api/v1/todos?completed=false&priority=high' \
  -H 'X-Session-Id: alice'
```

Mark a todo as done and clear its due date:

```bash
curl -X PATCH https://mocks.chapar.rest/api/v1/todos/{id} \
  -H 'X-Session-Id: alice' \
  -H 'Content-Type: application/json' \
  -d '{"completed": true, "due_at": null}'
```

## Utilities

| Method | Path | What it does |
|--------|------|--------------|
| `GET` `POST` `PUT` `PATCH` `DELETE` | `/echo` | Returns the request as the server received it: method, path, query, headers, body, and the body parsed as JSON when it is JSON |
| `GET` | `/status/{code}` | Responds with any status code from 200 to 599 |
| `GET` | `/delay/{ms}` | Waits up to 10000 ms, then echoes the request |
| `GET` | `/auth/basic/{user}/{password}` | `200` when Basic credentials match the path, otherwise `401` with a `WWW-Authenticate` challenge |
| `GET` | `/auth/bearer` | `200` for any `Authorization: Bearer <token>`, otherwise `401` |
| `GET` | `/auth/api-key` | `200` for any key in the `X-API-Key` header or `api_key` query parameter, otherwise `401` |
| `GET` | `/cookies` | Returns the cookies you sent |
| `GET` | `/cookies/set?name=value` | Sets a cookie for every query parameter |
| `GET` | `/redirect/{n}` | Redirects `n` times (1-10) with `302`, ending at `/echo` |
| `POST` | `/upload` | Accepts `multipart/form-data`; returns each file's name, size, content type and SHA-256, plus the form fields |
| `POST` | `/form` | Accepts `application/x-www-form-urlencoded`; returns the fields |
| `GET` | `/stream/{n}` | Streams `n` (1-100) newline-delimited JSON lines, 100 ms apart |
| `GET` | `/bytes/{n}` | Returns `n` random bytes (up to 100 KiB) as `application/octet-stream` |
| `GET` | `/formats/{format}` | The same sample document as `json`, `xml`, `html`, `text` or `csv`, with the matching content type |

### Examples

See exactly what your client sends:

```bash
curl -X POST 'https://mocks.chapar.rest/api/v1/echo?page=2' \
  -H 'Content-Type: application/json' \
  -d '{"hello": "world"}'
```

```json
{
  "method": "POST",
  "url": "/api/v1/echo?page=2",
  "path": "/api/v1/echo",
  "protocol": "HTTP/2.0",
  "host": "mocks.chapar.rest",
  "query": { "page": ["2"] },
  "headers": { "Content-Type": ["application/json"], "...": ["..."] },
  "body": "{\"hello\": \"world\"}",
  "json": { "hello": "world" }
}
```

Check how your client handles errors and timeouts:

```bash
curl -i https://mocks.chapar.rest/api/v1/status/503
curl https://mocks.chapar.rest/api/v1/delay/3000
```

Test each authentication type:

```bash
curl -u alice:secret https://mocks.chapar.rest/api/v1/auth/basic/alice/secret
curl -H 'Authorization: Bearer my-token' https://mocks.chapar.rest/api/v1/auth/bearer
curl -H 'X-API-Key: my-key' https://mocks.chapar.rest/api/v1/auth/api-key
```

Upload a file:

```bash
curl -F 'file=@photo.png' -F 'caption=holiday' https://mocks.chapar.rest/api/v1/upload
```
