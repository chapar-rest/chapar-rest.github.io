---
title: "Quick Start"
weight: 101
summary: "Send your first REST and gRPC requests in a few minutes"
---

This walkthrough uses the free [Chapar mock server](../../mockserver) at `mocks.chapar.rest`, so you don't need an API of your own. It takes about five minutes.

## 1. Learn the window

![The Chapar window](../../usingchapar/images/http-request.png)

- **Title bar**: the space you are working in (left), the command palette (middle), and on the right the About button, the cookie jar, the active environment and Settings.
- **Navigation bar** (far left): **Requests**, **Envs** (environments) and **Spaces**.
- **Sidebar**: the collections and requests of the current space. Click the panel button next to the space name to hide it.
- **Tabs**: every request, collection and environment you open gets a tab. A filled dot marks unsaved changes.
- **Request and response panes**: side by side or stacked. Switch with **Split** in the footer.
- **Footer**: the version, **Split**, the **Console** with Chapar's logs, and **Notifications**.

## 2. Create an environment

An environment holds the values your requests share, such as a base URL or a token.

1. Click **Envs** in the navigation bar, then **New**.
2. Click the name at the top of the new tab and call it `Mock`.
3. Click **Add** twice and fill in two variables:

   | Key | Value |
   |-----|-------|
   | `baseUrl` | `https://mocks.chapar.rest/api/v1` |
   | `sessionId` | any name of your own, for example `alice-laptop` |

4. Press **⌘S** (**Ctrl+S** on Windows and Linux) to save, and pick **Mock** in the environment selector in the title bar.

See [Environments](../../usingchapar/environments) for more.

## 3. Send an HTTP request

1. Go back to **Requests** and click **New**. A request called *New Request* opens in a tab.
2. Keep the method `GET` and type `{{baseUrl}}/todos` as the URL. While you type `{{`, Chapar suggests the variables of the active environment.
3. Open the **Headers** tab and add `X-Session-Id` with the value `{{sessionId}}`. This gives you a private todo list on the mock server.
4. Click **Send** or press **⌘Enter**.

The response pane shows the status, time and size, and the JSON body. The **Headers**, **Cookies** and **Timeline** tabs show the rest of the exchange. Save the request with **⌘S** and rename it on its **Info** tab.

Read [HTTP Requests](../../usingchapar/http-requests) for every option.

## 4. Invoke a gRPC method

1. Download [`todo.proto`](https://github.com/chapar-rest/mock-server/blob/main/api/proto/mock/v1/todo.proto) from the mock server repository.
2. Click the arrow next to **New** and choose **gRPC request**.
3. Type `mocks.chapar.rest:443` as the address.
4. On the **Server** tab choose **Proto files**, click **Add proto files…** and pick `todo.proto`. Chapar loads the methods.
5. Pick `/mock.v1.TodoService/ListTodos` in the method list, and add `x-session-id` with your session name on the **Metadata** tab.
6. Click **Invoke**.

![A gRPC request](../../usingchapar/images/grpc-request.png)

Read [gRPC Requests](../../usingchapar/grpc-requests) for reflection, TLS, streaming and more.

## 5. Chain requests

Most APIs need a value from one response in the next request, like an id or a token. Chapar can:

- **Extract** values from a response into the environment, with a JSONPath, a header or a cookie name.
- **Trigger** another request before this one is sent.
- Run a **Python script** before or after the request, with tests.

See [Request Actions](../../usingchapar/request-actions) and [Python Scripting](../../scripting).

## Shortcuts worth knowing

| Shortcut (macOS) | Windows / Linux | Action |
|------------------|-----------------|--------|
| **⌘K** | **Ctrl+K** | Command palette: open any request, collection or environment, run any command |
| **⌘Enter** | **Ctrl+Enter** | Send the request / invoke the method |
| **⌘S** | **Ctrl+S** | Save the active tab |
| **⌘,** | **Ctrl+,** | Settings |
| **⌘P** | **Ctrl+P** | Go to an open tab |
| **⌘W** | **Ctrl+W** | Close the tab |
| **⌘⇧]** / **⌘⇧[** | **Ctrl+Shift+]** / **Ctrl+Shift+[** | Next / previous tab |
| **⌘1** … **⌘8**, **⌘9** | **Ctrl+1** … **Ctrl+8**, **Ctrl+9** | Jump to a tab, or to the last one |
