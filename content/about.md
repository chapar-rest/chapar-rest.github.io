---
title: "About"
summary: "About Chapar"
---

### What is Chapar?

Chapar is a fast, native API client for REST, gRPC and GraphQL, built with Go. It draws its UI on the GPU with [Yoga](https://github.com/mirzakhany/yoga), so it starts quickly, stays light and runs the same on macOS, Linux and Windows without a browser engine. Your requests, environments and cookies are plain files on your machine: no account, no cloud sync and no telemetry.

### Project Goals

The goal of this project is to make it easy for individuals and teams to test their APIs with ease. Chapar focuses on speed, privacy and keeping your API collections as files you own, next to your code.

### What Chapar means?
Chapar was the institution of the royal mounted couriers in ancient Persia.
The messengers, called Chapar, alternated in stations a day's ride apart along the Royal Road.
The riders were exclusively in the service of the Great King and the network allowed for messages to be transported from Susa to Sardis (2699 km) in nine days; the journey took ninety days on foot.

Herodus described the Chapar as follows:

> There is nothing in the world that travels faster than these Persian couriers. Neither snow, nor rain, nor heat, nor darkness of night prevents these couriers from completing their designated stages with utmost speed.
>
> Herodotus, about 440 BC

### How is Chapar implemented?

Chapar is a single binary written in Go. Its UI is built with [Yoga](https://github.com/mirzakhany/yoga), a GPU-rendered (WebGPU) UI toolkit. Python scripts run in a sandboxed [executor](https://github.com/chapar-rest/python-executor) container.

### Current Status

Chapar is under active development. Version 0.7.0 moved the app to a new GPU-rendered UI; WebSocket and MQTT support are next on the list.

### Features

- **REST / HTTP**: every method, query and path params, JSON, XML, text, form-data, URL-encoded and binary bodies.
- **gRPC**: server reflection or proto files, unary and server-streaming calls, metadata and trailers, TLS and mutual TLS, and example messages for any method.
- **GraphQL**: queries and variables, with data and errors split out.
- **Spaces** keep separate collections, requests and environments.
- **Collections** share headers, auth and notes with their requests.
- **Environments** with `{{variables}}`, completion, and secret values encrypted with a key in your OS keychain.
- **Cookie jar** per environment, which you can browse and edit.
- **Request actions**: trigger another request first, set environment values from a response, extract values with JSONPath.
- **Python scripting**: pre and post-request scripts for HTTP, gRPC and GraphQL, with tests and logs.
- **Timeline** of every request: DNS, connect, TLS, time to first byte and scripts.
- **Code generation** for cURL, Python, Go, JavaScript, Java, Ruby and .NET.
- **Import** Postman collections and environments, OpenAPI specs and proto files.
- **Command palette**, themes, language servers for code editors, and a console.
- Everything is stored as YAML files, so a space can live in git.

### Who is behind this project?

The project was started by [Mohsen Mirzakhani](https://www.linkedin.com/in/mirzakhany). Mohsen's background has been in software development and architecture. Chapar was started to find ways to simplify and expedite the testing process for developers.

### How to stay in touch?

- Star the repo at [github.com/chapar-rest/chapar](https://github.com/chapar-rest/chapar)
- Email at [support@chapar.rest](mailto:support@chapar.rest)
- Follow on [X](https://x.com/ChaparRest)
- Connect on [Slack](https://gophers.slack.com/messages/chapar)
- Subscribe to YouTube channel [Chapar](https://www.youtube.com/channel/UCn7EZpdKM8SWE0JcVS3ZXrQ)
