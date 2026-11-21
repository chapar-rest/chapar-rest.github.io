---
title: "GraphQL Requests"
weight: 105
summary: "Send GraphQL queries and mutations with variables, headers and auth, and read the response"
---

A GraphQL request sends a query or mutation to a GraphQL endpoint. Create one from **New › GraphQL request** above the request tree, or right-click a collection and choose **New GraphQL request**.

## The request bar

| Control | What it does |
|---------|--------------|
| **URL** | The GraphQL endpoint, for example `https://api.example.com/graphql`. `{{variables}}` work here. Press **Enter** in the field to send. |
| **Send** | Sends the request (**⌘Enter**). |
| **Save** | Saves the request (**⌘S**). Enabled when there are unsaved changes. |

Chapar sends the request as a `POST` with a JSON body, `{"query": …, "variables": …}`, and `Content-Type: application/json`, which every GraphQL server accepts.

## Query

Write the query or mutation on the **Query** tab:

```graphql
query Launches($limit: Int) {
  launches(limit: $limit) {
    id
    mission_name
  }
}
```

`{{variables}}` and [functions](../functions) are filled in before the request is sent, and the editor completes them as you type.

## Variables

The **Variables** tab holds the GraphQL variables as a JSON object, for example `{"limit": 5}`. It is a JSON editor, so it formats, highlights and checks the JSON. Leave it as `{}` to send no variables; invalid JSON stops the request with an error that says so.

Environment `{{variables}}` work inside the JSON too, so `{"userId": "{{userId}}"}` takes the value from the active environment.

## Headers, Auth, Actions and Info

These tabs work the same as for [HTTP requests](../http-requests):

| Tab | What it does |
|-----|--------------|
| **Headers** | Extra headers, each with a checkbox to turn it off. Headers set on the request's [collection](../requests-and-collections#collection-settings) are sent too. |
| **Auth** | **Inherit** (use the collection's auth), **None**, **Bearer**, **Basic** or **API Key**. |
| **Actions** | Pre-request and post-request [actions](../request-actions): run a request or a [Python script](../../scripting) first, and set environment values from the response. |
| **Info** | The request's name and description. A request without a name is listed by its URL. |

## The response

The line at the top of the response pane shows the **status code**, the **time** the request took and the **size** of the body.

| Tab | Shows |
|-----|-------|
| **Response** | The JSON the server returned, formatted and highlighted. Check **Raw** to see it exactly as received. Right-click for **Copy** and **Save…**. |
| **Headers** | The headers sent and the headers the server returned. |
| **Cookies** | Cookies the response set and the cookies sent from the jar. See [Cookies](../cookies). |
| **Timeline** | Every step of the request on one time scale: actions, DNS lookup, connect, TLS, sending, waiting and download. |

GraphQL servers usually answer `200` even when a query fails, with the problem in the body's `errors` array, so check the body as well as the status.
