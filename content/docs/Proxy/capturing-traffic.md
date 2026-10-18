---
title: "Capturing Traffic"
weight: 1
summary: "Start the proxy, send an app's traffic through it, and filter, read and reuse what it captures"
---

## Start the proxy

Open **Proxy** in the left bar and click **Start**. The header shows **Listening** and the address to use, `127.0.0.1:9090` by default. **Stop** closes the proxy and every connection through it.

| Button | What it does |
|--------|--------------|
| **Setup** | Opens the [Setup guide](../certificates). Its shield turns green once HTTPS capture works on this computer. |
| **Pause** | Stops recording new requests. Traffic keeps flowing through the proxy; **Resume** records again. |
| **Export HAR** | Saves the requests shown in the table as a HAR file. |
| **Clear** | Removes every captured request. |

Start, Stop, Clear and the Setup guide are in the command palette too.

## Send traffic through it

An app only goes through the proxy when it is told to:

- **The whole computer**: set the HTTP and HTTPS proxy to `127.0.0.1:9090` in your system's network settings. On macOS that's **System Settings › Network › (your network) › Details › Proxies**. Browsers and most apps follow it. Turn it off again when you're done.
- **One app**: many apps and tools have their own proxy setting.
- **Terminal tools**: the Setup guide has a snippet to paste in your shell. It sets `HTTP_PROXY` and `HTTPS_PROXY`, and makes curl, Node and Python trust Chapar's certificate:

  ```sh
  export HTTP_PROXY=http://127.0.0.1:9090 HTTPS_PROXY=http://127.0.0.1:9090
  export SSL_CERT_FILE="$HOME/Library/Application Support/chapar/proxy/ca-cert.pem" …
  curl https://mocks.chapar.rest/api/v1/todos
  ```

- **Phones and other devices**: see [Certificates and Devices](../certificates#phones-and-other-devices).

## The request table

Each request is a row with its method, status, host, path, content type, response size, time and the app that sent it. The newest is at the top. Status codes are colored: green for `2xx`, blue for `3xx`, yellow for `4xx` and red for `5xx` and failed requests. A lock marks HTTPS.

On macOS and Linux, Chapar names the app on this computer that opened each connection, such as *Google Chrome* or *curl*. Requests from other devices are listed as *Device* and their address. When neither is known, the name comes from the User-Agent.

## Filter

- **Sidebar**: **All traffic**, your **Pinned** apps and domains, every **App** and every **Domain** seen so far. Click one to show only its requests. The field above it filters the sidebar.
- **Buttons above the table**: **HTTP**, **HTTPS** and **WS** by protocol; **JSON**, **XML**, **HTML**, **CSS**, **JS**, **Images**, **Media**, **Fonts**, **Forms** and **Other** by content type; and **Errors** for failed requests and `4xx`/`5xx` responses. Buttons in the same group add up, and **All** turns them off.
- **Search**: the field at the right matches the URL, method, status or app.

Right-click an app or domain in the sidebar to **Pin** it, to **Remove its requests**, or for a domain, **Don't decrypt this domain**.

## Read a request

Click a row to see the request and its response side by side.

| Request tab | Shows |
|-------------|-------|
| **Headers** | The request headers. |
| **Query** | The query parameters, one per line. |
| **Body** | The body, with JSON indented and highlighted. |
| **Raw** | The request line, headers and body as sent. |

The response shows its status, size and time, with **Headers**, **Body** and **Raw** tabs. Compressed bodies (gzip, brotli, deflate, zstd) are shown decoded, and binary bodies such as images are summarized. Right-click a body to copy it or save it to a file.

## Reuse a request

The **…** menu at the top right of the request has:

- **Open in Chapar**: saves the request as a new request in your workspace and opens it, with its method, URL, query, headers, body and auth. Edit it and send it again like any request.
- **Copy as cURL**: copies a `curl` command that sends the same request. Every value is quoted, so it pastes safely into a terminal.

## Export HAR

**Export HAR** saves the requests the table shows, so the sidebar selection, buttons and search decide what goes in. The file uses the [HAR 1.2](http://www.softwareishard.com/blog/har-12-spec/) format that browsers' developer tools and many other tools open. Requests still waiting for their response aren't included.

## Domains that aren't decrypted

Some apps pin their certificates: they only accept their own server's certificate and refuse Chapar's, so their requests fail through the proxy. List those domains under **Don't decrypt** in [Settings › Proxy](../../usingchapar/settings#proxy), or right-click the domain in the sidebar and choose **Don't decrypt this domain**. Their traffic then passes through untouched, and the table shows only that a connection was made. Apple's domains are on the list from the start.

## Limits

- HTTPS to an IP address, such as `https://192.0.2.10`, can't be decrypted: clients don't say which site they want when they connect by IP.
- WebSocket connections through the proxy are listed, but their messages aren't shown yet.
- HTTP/3 isn't supported. Browsers fall back to HTTP/1.1 or HTTP/2 when they go through a proxy.
