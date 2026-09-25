---
title: "Import And Export Data"
weight: 102
summary: "Import Postman, OpenAPI and proto files, and find where Chapar keeps your data"
---

## Import

Chapar imports:

| Format | Where | Result |
|--------|-------|--------|
| Postman collection (`.json`, v2.1) | **Requests** › **Import** | A collection with its requests |
| OpenAPI specification (`.json`, `.yaml`, `.yml`) | **Requests** › **Import** | A collection with a request for every operation |
| Protobuf file (`.proto`) | **Requests** › **Import** | A collection with a gRPC request for every method |
| Postman environment (`.json`) | **Envs** › **Import** | An environment with its variables |

To import a collection, click **Import** above the request tree (or right-click the tree and choose **Import**), and pick the file. Chapar recognizes the format from the file.

![The request tree menu](../../usingchapar/images/tree-menu.png)

To import an environment, open **Envs**, click **Import** and pick a Postman environment file.

{{< callout type="info" >}}
Try it with the mock server: import its [OpenAPI spec](https://raw.githubusercontent.com/chapar-rest/mock-server/main/api/rest/openapi.yaml) to get a ready-made request for every REST endpoint, or its [proto files](https://github.com/chapar-rest/mock-server/tree/main/api/proto/mock/v1) for the gRPC services.
{{< /callout >}}

Insomnia and HAR imports are planned.

## Where your data lives

Everything you create is stored as YAML files, one file per request and environment, in the **workspace folder**:

{{< tabs items="macOS,Linux,Windows" >}}
  {{< tab >}}`~/.config/chapar`{{< /tab >}}
  {{< tab >}}`~/.config/chapar` (or `$XDG_CONFIG_HOME/chapar`){{< /tab >}}
  {{< tab >}}`%APPDATA%\chapar`{{< /tab >}}
{{< /tabs >}}

Inside it, every [space](../../usingchapar/workspaces) has its own folder:

```text
chapar/
└── Chapar Mock/                  # a space
    ├── _workspace.yaml
    ├── collections/
    │   └── Todos/
    │       ├── _collection.yaml  # the collection's headers, auth and notes
    │       ├── Create todo.yaml
    │       └── List todos.yaml
    ├── requests/                 # requests that are not in a collection
    ├── envs/
    │   ├── Local.yaml
    │   └── Production.yaml
    └── .state/                   # machine-local state, ignored by git
        └── cookies/
```

Chapar's own settings are kept apart from your data:

| OS | Settings folder |
|----|-----------------|
| macOS | `~/Library/Application Support/chapar` |
| Linux | `~/.config/chapar` |
| Windows | `%APPDATA%\chapar` |

### Change the workspace folder

Open **Settings** (**⌘,**) › **Data** and set **Workspace path** to the absolute path of another folder, for example a folder inside a git repository or a synced drive. Click **Save** and restart Chapar.

## Export and share

Because a space is a folder of YAML files, you share it by sharing the folder. Keep it in git to review and version your API collections with your code:

```bash
cd ~/.config/chapar
git init
git add .
git commit -m "Add Chapar collections"
```

The `.state` folder, which holds your cookie jars, comes with its own `.gitignore`, so session tokens stay out of the repository.

To share a single request in another form, open it and click **Code**: Chapar writes it as cURL, Python, Go, JavaScript (Axios or Node fetch), Java (OkHttp), Ruby or .NET code. See [Generate code](../../usingchapar/http-requests#generate-code).

{{< callout type="warning" >}}
Environment values are stored in plain text unless you mark them **secret**. Secret values are encrypted with a key kept in your OS keychain, so environment files are safe to commit. See [Secret values](../../usingchapar/environments#secret-values).
{{< /callout >}}
