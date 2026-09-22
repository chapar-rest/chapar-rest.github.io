---
title: "gRPC API"
weight: 2
summary: "TodoService and UtilityService of the mock server"
---

Address: `mocks.chapar.rest:443` over TLS. The services are in the `mock.v1` package, defined in [`todo.proto`](https://github.com/chapar-rest/mock-server/blob/main/api/proto/mock/v1/todo.proto) and [`utility.proto`](https://github.com/chapar-rest/mock-server/blob/main/api/proto/mock/v1/utility.proto).

{{< callout type="warning" >}}
Server reflection is enabled, but through our CDN it is unreliable and often times out. Import the proto files instead of relying on reflection. For the same reason, a bidirectional stream that waits for a reply before sending its next message may hang. Streams that send all their messages and then close work reliably.
{{< /callout >}}

## TodoService

The same todo list as the [REST API](../rest-api), with the same rules and data. Send `x-session-id` metadata to use your own session (see [Sessions](../#sessions)).

| RPC | Description |
|-----|-------------|
| `ListTodos` | List todos. Filters: `completed`, `priority`, `query`, `limit`, `offset` |
| `GetTodo` | Get a todo by `id` |
| `CreateTodo` | Create a todo; priority defaults to medium |
| `UpdateTodo` | Update the fields named in `update_mask`; an empty mask replaces the whole todo |
| `DeleteTodo` | Delete a todo |
| `ResetTodos` | Restore the session's sample todos |

Priorities are the enum values `TODO_PRIORITY_LOW`, `TODO_PRIORITY_MEDIUM` and `TODO_PRIORITY_HIGH`. Errors use standard status codes: `INVALID_ARGUMENT` for bad input, `NOT_FOUND` for an unknown id, and `RESOURCE_EXHAUSTED` when the session holds 100 todos.

## UtilityService

| RPC | Type | What it does |
|-----|------|--------------|
| `Echo` | Unary | Returns your message and the request metadata; also sends a `method` header and a `timestamp` trailer |
| `Status` | Unary | Fails with the requested status `code` (0-16) and `message`; `0` succeeds |
| `Delay` | Unary | Responds after `ms` milliseconds (up to 10000) |
| `Auth` | Unary | Succeeds when `authorization` (Basic or Bearer) or `x-api-key` metadata is sent, otherwise `UNAUTHENTICATED` |
| `ServerStream` | Server streaming | Sends `count` (1-100) messages, `interval_ms` (0-5000) apart |
| `ClientStream` | Client streaming | Collects every message and replies with the count once you close the stream |
| `BidiStream` | Bidirectional | Echoes each message as it arrives |

## Examples

These examples use [grpcurl](https://github.com/fullstorydev/grpcurl) with the proto files downloaded from the repository into `./proto`.

Create a todo in your own session:

```bash
grpcurl -import-path ./proto -proto mock/v1/todo.proto \
  -H 'x-session-id: alice' \
  -d '{"title": "Buy milk", "priority": "TODO_PRIORITY_HIGH"}' \
  mocks.chapar.rest:443 mock.v1.TodoService/CreateTodo
```

Mark it as completed without touching other fields:

```bash
grpcurl -import-path ./proto -proto mock/v1/todo.proto \
  -H 'x-session-id: alice' \
  -d '{"todo": {"id": "<id>", "completed": true}, "updateMask": {"paths": ["completed"]}}' \
  mocks.chapar.rest:443 mock.v1.TodoService/UpdateTodo
```

{{< callout type="info" >}}
In JSON a field mask is normally a string, such as `"updateMask": "completed"`. grpcurl only accepts the object form shown above; the server accepts both.
{{< /callout >}}

Check how your client shows an error status:

```bash
grpcurl -import-path ./proto -proto mock/v1/utility.proto \
  -d '{"code": 7, "message": "not allowed"}' \
  mocks.chapar.rest:443 mock.v1.UtilityService/Status
```

Receive a server stream of three messages:

```bash
grpcurl -import-path ./proto -proto mock/v1/utility.proto \
  -d '{"message": "tick", "count": 3, "interval_ms": 500}' \
  mocks.chapar.rest:443 mock.v1.UtilityService/ServerStream
```

See your headers, the response headers and trailers with `-v`:

```bash
grpcurl -v -import-path ./proto -proto mock/v1/utility.proto \
  -H 'x-trace-id: 42' -d '{"message": "hi"}' \
  mocks.chapar.rest:443 mock.v1.UtilityService/Echo
```
