---
title: "Settings"
weight: 108
summary: "Every setting: HTTP defaults, appearance, scripting, editor, language servers, security and data"
---

Open **Settings** with the gear in the title bar or **⌘,** (**Ctrl+,**). Changes apply when you click **Save**; **Cancel** drops them and **Defaults** resets the form to Chapar's defaults. Appearance changes preview live.

## General

![General settings](../images/settings-general.png)

| Setting | Default | What it does |
|---------|---------|--------------|
| **HTTP version** | HTTP/1.1 | The HTTP version for HTTP requests: HTTP/1.1 or HTTP/2. |
| **Request timeout (sec)** | 30 | How long to wait for a response. `0` waits forever. |
| **Response size (MB)** | 10 | The largest response body Chapar reads. `0` means no limit. |
| **Follow redirects** | On | Follow `3xx` redirects. |
| **Validate TLS certificates** | On | Check HTTPS certificates. Turn off for servers with self-signed certificates. |
| **Send no-cache header** | On | Add `Cache-Control: no-cache` to requests. |
| **Send Chapar agent header** | On | Send `User-Agent: Chapar/<version>`. |

## Appearance

![Appearance settings](../images/settings-appearance.png)

| Setting | What it does |
|---------|--------------|
| **Theme** | The color theme: light and dark themes including Tokyo Night, Catppuccin, Dracula, Nord, Gruvbox, One, Solarized and GitHub. Every theme meets WCAG 2.1 AA contrast. |
| **UI font size** | The size of the app's text. |
| **Editor font size** | The size of text in code editors. |
| **Horizontal request/response split** | Stack the request above the response instead of side by side. **Split** in the footer toggles it too. |
| **Hide navbar** | Hide the Requests / Envs / Spaces bar on the left. The pages stay reachable from the command palette. |

## Scripting

Turns the Python executor on or off and configures it. See [Python Scripting › Setup](../../scripting#setup).

## Editor

| Setting | Default | What it does |
|---------|---------|--------------|
| **Font family** | JetBrains Mono | The code editor font. |
| **Font size** | 12 | The code editor text size. |
| **Indentation** | Spaces | Indent with spaces or tabs. |
| **Tab width** | 4 | The width of a tab stop. |
| **Auto close brackets** / **Auto close quotes** | On | Insert the closing bracket or quote as you type. |
| **Show line numbers** | On | Show the line number gutter. |
| **Wrap lines** | On | Soft-wrap long lines. |
| **Syntax highlighting limit (KB)** | 2048 | Documents larger than this are shown without colors, because a highlighted document takes many times its size in memory. Large responses offer a **Highlight anyway** button. |

## Language servers

Language servers add completion, hover documentation and diagnostics to code editors. Python (for scripts) uses [pyright](https://github.com/microsoft/pyright), which knows Chapar's `chapar`, `request` and `response` objects. You can also turn on servers for JSON and XML bodies and for the languages of generated code.

![Language server settings](../images/settings-language-servers.png)

For each language you can turn the server on or off, and set the **Command** and **Arguments** that start it. The status line shows whether it runs. When a server is missing, Chapar offers to **Install** it; **Restart** restarts it with the saved settings.

| Language | Default command |
|----------|-----------------|
| Python | `pyright-langserver --stdio` |
| JSON | `vscode-json-language-server --stdio` |
| XML | `lemminx` |
| Go | `gopls serve` |
| JavaScript | `typescript-language-server --stdio` |
| Java | `jdtls` |
| C# | `csharp-ls` |
| Bash | `bash-language-server start` |
| Ruby | `solargraph stdio` |

A server starts when the first editor of its language is shown and stops when the last one closes.

## Security

The key that encrypts [secret environment values](../environments#secret-values). Set up a key, show it, replace it with a key you paste, or remove it from this machine. The key is kept in your OS secret store and never written to disk by Chapar.

![Security settings](../images/settings-security.png)

## Data

**Workspace path** is the folder where your spaces are stored. Change it to keep your collections in a git repository or a synced folder, then restart Chapar. See [Where your data lives](../../gettingstarted/import-and-export-data#where-your-data-lives).
