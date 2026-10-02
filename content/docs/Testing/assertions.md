---
title: "Assertions and Captures"
weight: 2
summary: "Every target and operator a step can check, how values compare, captures, and how variables resolve during a run"
---

## Assertions

An assertion reads one value from the response, a **target**, and compares it with an expected value.

| Target | In the editor | Selector | Actual value |
|--------|---------------|----------|--------------|
| `status` | Status | | HTTP status code, or the gRPC status code for gRPC requests (`0` is OK) |
| `header` | Header | `key` | A response header, case-insensitive |
| `body` | Body (JSON) | `path` | A [JSONPath](https://goessner.net/articles/JsonPath/) into the JSON body, such as `$.data.id` |
| `text` | Body text | | The raw body text |
| `cookie` | Cookie | `key` | A cookie the response set |
| `time` | Time (ms) | | The response time in milliseconds |
| `size` | Size (bytes) | | The response size in bytes |
| `metadata` | gRPC metadata | `key` | gRPC response metadata, case-insensitive |
| `trailer` | gRPC trailer | `key` | A gRPC trailer, case-insensitive |

| Operator | In the editor | Passes when |
|----------|---------------|-------------|
| `eq` / `ne` | equals / not equals | The values are (not) equal. JSON values compare deeply, and a number equals a numeric string, since header values are always text. |
| `exists` / `notExists` | exists / not exists | The key or path is (not) there. A JSON `null` exists. |
| `contains` / `notContains` | contains / not contains | The text contains the value, or the array has it as an element. |
| `in` | one of | The actual value equals one element of a list, such as `[200, 201]`. |
| `gt` `gte` `lt` `lte` | > ≥ < ≤ | Numeric comparison. Numeric strings count as numbers. |
| `matches` | matches | A [Go regular expression](https://pkg.go.dev/regexp/syntax) matches the actual value as text. |
| `type` | type is | The JSON type is `string`, `number`, `boolean`, `null`, `array` or `object`. |
| `length` | length | A string, array or object has this length. |

Rules worth knowing:

- Every operator except **exists** and **not exists** fails with *not found* when the selector matches nothing.
- Paths with wildcards or deep scans, such as `$.data[*].id` or `$..id`, return a list, which may be empty. Check them with **length** or **contains** rather than **exists**: `$.data[*].id` **contains** `{{todoId}}` checks that the list has the new todo.
- String values, and strings in a **one of** list, fill in `{{variables}}`: `$.data.id` **equals** `{{todoId}}`.
- Body numbers are read as 64-bit floats, so integers above 2<sup>53</sup> lose precision. Compare those with **Body text** and **matches**.
- Tests a request's own [post-request script](../../scripting) records with `@chapar.test` show up next to the assertions, and fail the step the same way.

## Captures

A capture stores a value from the response in a variable for the steps after it. It reads from the same targets as assertions, except time and size:

| From | Selector | Example |
|------|----------|---------|
| Body (JSON) | `path` | `$.data.id` → `todoId` |
| Header | `key` | `Location` → `newUrl` |
| Cookie | `key` | `session` → `sid` |
| Status | | → `lastStatus` |
| Body text | | the whole body |
| gRPC metadata, gRPC trailer | `key` | `x-request-id` → `requestId` |

A captured value is stored as text; numbers, lists and objects are stored as JSON. A capture that finds nothing fails the step, so a missing id stops the run before the next request sends `{{todoId}}` as it is.

## Where variables come from

When a step sends its request, `{{name}}` is filled in from the first of these that has it:

1. The step's own **Request overrides** variables (for this step only).
2. The run's variables: the case's **Variables**, then captures and the changes the requests' scripts and extract rules made, as the run goes.
3. The environment the run started with: the active one in the app, or `--env` on the command line.
4. The [built-in functions](../../usingchapar/functions), such as `{{randomUUID4}}` and `{{timeNow}}`.

Because the requests' own post-request actions feed the run's variables, a chain that works with **Send** in the app works in a test case too.

## The file format

A test case is a YAML file in the workspace's `testcases` folder. The app writes it for you, and the **YAML** tab shows it; you can also write one by hand:

```yaml
apiVersion: v1
kind: TestCase
metadata:
  id: 7f3c1e0a-5b0e-4c2b-9a51-2b7c1f0d9e11
  name: Todo lifecycle
spec:
  description: Creates a todo, reads it back and finds it in the list, then deletes it.
  tags: [smoke, todos]
  options:
    timeout: 10s              # default for each step
    continueOnFailure: false  # keep running steps after one fails
    persistEnv: false         # save the requests' env changes
  variables:
    - key: title
      value: Release notes
    - key: ciToken
      from: { osEnv: CI_TOKEN }
  setup:
    - id: reset
      request: { ref: Todos/Reset todos }
      assert:
        - { target: status, op: eq, value: 200 }
  steps:
    - id: create
      name: Create todo
      request: { ref: Todos/Create todo }
      assert:
        - { target: status, op: eq, value: 201 }
        - { target: body, path: $.data.id, op: exists }
        - { target: body, path: $.data.priority, op: eq, value: high }
        - { target: body, path: $.data.tags, op: length, value: 2 }
        - { target: header, key: Content-Type, op: contains, value: json }
      capture:
        - { var: todoId, from: body, path: $.data.id }
    - id: get
      request: { ref: Todos/Get todo }
      retry: { count: 3, delay: 1s }
      assert:
        - { target: body, path: $.data.id, op: eq, value: "{{todoId}}" }
        - { target: time, op: lt, value: 1000 }
    - id: list
      request: { ref: Todos/List todos }
      with:                   # overrides for this step only
        query: [{ key: limit, value: "100" }]
      assert:
        - { target: body, path: "$.data[*].id", op: contains, value: "{{todoId}}" }
  teardown:
    - id: delete
      request: { ref: Todos/Delete todo }
      assert:
        - { target: status, op: eq, value: 204 }
```

A step finds its request by `request.id` first, then by `request.ref`: `Collection/Request` for a request in a collection, or the name of a standalone request. The app always fills in `id`; `ref` is handy in hand-written files. Step ids must be unique within the case.

Other step fields: `name`, `timeout`, `continueOnFailure`, `disabled: true`, and under `with`: `variables` (a map), `headers`, `query` and `body` (the HTTP body, the GraphQL query or the gRPC message).
