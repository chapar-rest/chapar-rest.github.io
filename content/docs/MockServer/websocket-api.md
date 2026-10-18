---
title: "WebSocket API"
weight: 3
summary: "Echo, streams, live todo updates, close codes and auth over WebSocket"
---

The WebSocket endpoints live at `wss://mocks.chapar.rest/ws`. Open them with a [WebSocket request](../../usingchapar/websocket-requests) in Chapar.

| Endpoint | What it does |
|----------|--------------|
| `/ws/echo` | Sends every message back unchanged, text or binary. |
| `/ws/stream?count=10&interval_ms=1000` | Sends `{"index","count","time"}` `count` times (1-100), `interval_ms` apart (0-5000), then closes with code 1000. |
| `/ws/todos` | Live changes to a session's todos, as REST and gRPC calls make them. |
| `/ws/close?code=4000&reason=bye` | Closes right away with that code (1000-1003, 1007-1011, 3000-4999) and reason. |
| `/ws/auth` | Needs credentials (see below), says which one it accepted, then echoes. |

## Live todo updates

`/ws/todos` follows one [session](../#sessions), chosen by the `X-Session-Id` header or the `session` query parameter. It sends `{"type":"subscribed"}` first, then a message for each change:

| `type` | Sent when |
|--------|-----------|
| `todo.created` | A todo is created. |
| `todo.updated` | A todo is updated. |
| `todo.deleted` | A todo is deleted. |
| `todos.reset` | The session is reset with `POST /todos/reset`. |

Each change message has the todo's `id` and the `todo` itself.

To try it, connect a WebSocket request to `wss://mocks.chapar.rest/ws/todos` with an `X-Session-Id` header, then create or update todos from a REST or gRPC request with the same session id. The changes arrive in the message log.

The endpoint ignores messages you send to it, as does `/ws/stream`.

## Auth

`/ws/auth` accepts any one of:

- an `Authorization` header with a Bearer token or Basic credentials,
- an `X-API-Key` header,
- an `access_token` or `api_key` query parameter.

Without credentials the handshake fails with `401`.

## Connections

- Every endpoint offers the `chapar.v1` subprotocol and `permessage-deflate` compression.
- The server pings every 30 seconds, accepts messages up to 64 KiB, and closes connections after 10 minutes.
- Each client may hold 5 connections at once.
- Bad query parameters fail the handshake with a JSON error, like the REST API.
