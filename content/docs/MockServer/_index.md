---
title: Mock Server
sidebar:
  open: true
weight: 103
---

`mocks.chapar.rest` is a free, public mock API you can use to try Chapar, or any other API client, without running a server of your own. The same server speaks REST and gRPC, and GraphQL, WebSocket and MQTT support are planned.

| Protocol | Address | Contract |
|----------|---------|----------|
| REST | `https://mocks.chapar.rest/api/v1` | [OpenAPI spec](https://github.com/chapar-rest/mock-server/blob/main/api/rest/openapi.yaml) |
| gRPC | `mocks.chapar.rest:443` (TLS) | [Proto files](https://github.com/chapar-rest/mock-server/tree/main/api/proto/mock/v1) |

It offers two kinds of endpoints:

- **Todos**: an in-memory todo list with full create, read, update and delete support, for trying out real request flows.
- **Utilities**: endpoints that echo your request back, return any status code, wait, check credentials, set cookies, redirect, accept uploads and stream responses, for checking how a client behaves.

{{< cards >}}
{{< card link="rest-api" title="REST API" subtitle="Todo and utility endpoints over HTTP" icon="globe-alt" >}}
{{< card link="grpc-api" title="gRPC API" subtitle="TodoService and UtilityService, including streaming" icon="lightning-bolt" >}}
{{< /cards >}}

## Use it from Chapar

The quickest start is to import the server's contracts. See [Import And Export Data](../gettingstarted/import-and-export-data):

1. **REST**: import the [OpenAPI spec](https://raw.githubusercontent.com/chapar-rest/mock-server/main/api/rest/openapi.yaml) to get a ready-made request for every endpoint.
2. **gRPC**: import the [proto files](https://github.com/chapar-rest/mock-server/tree/main/api/proto/mock/v1) (`todo.proto` and `utility.proto`) and point the requests at `mocks.chapar.rest:443`.

Then create an [environment](../usingchapar/environments) with two variables:

| Variable | Value |
|----------|-------|
| `baseUrl` | `https://mocks.chapar.rest/api/v1` |
| `sessionId` | any name of your own, e.g. `alice-laptop` |

Send `X-Session-Id: {{sessionId}}` with your todo requests (as `x-session-id` metadata for gRPC) to get a private todo list.

## Sessions

Todos belong to a **session**, chosen by the `X-Session-Id` header (REST) or the `x-session-id` metadata (gRPC):

- Requests with the same session id share one todo list. Requests without one use the shared `public` session, which anyone can change.
- A session id is 1-64 characters of letters, digits, `.`, `_` or `-`.
- A new session starts with three sample todos, so your first list request already returns data. `POST /todos/reset` (or the `ResetTodos` RPC) puts them back.
- REST and gRPC share the same data: a todo created over gRPC can be read over REST with the same session id.

{{< callout type="info" >}}
All data lives in memory. A session is deleted after one hour without requests, and everything is lost whenever the server restarts. Don't store anything you want to keep.
{{< /callout >}}

## Limits

The server is shared by everyone, so it enforces these limits:

| Limit | Value | When exceeded |
|-------|-------|---------------|
| Request rate | 10 requests/second per client, bursts up to 30 | `429 Too Many Requests` |
| Concurrent requests | 10 per client, including open streams | `429 Too Many Requests` |
| Todos per session | 100 | `409 Conflict` / `RESOURCE_EXHAUSTED` |
| Active sessions | 1000 (the `public` session is always available) | `503 Service Unavailable` / `UNAVAILABLE` |
| Request body | 10 MiB | `400 Bad Request` |

The server is open source at [chapar-rest/mock-server](https://github.com/chapar-rest/mock-server). You can also run it locally with `go run ./cmd/server` (it needs `MOCK_ENV=local`) and use `http://localhost:8080/api/v1` without any limits.
