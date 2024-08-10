---
weight: 999
title: "Overview"
description: ""
icon: "article"
icon: "circle"
date: "2024-02-25T15:13:21+01:00"
lastmod: "2024-02-25T15:13:21+01:00"
draft: false
toc: true
---

# Chapar - Native API Testing Tool

Chapar is an upcoming native API testing tool built with Go, designed to simplify and expedite the testing process for developers. While still in its early beta phase, Chapar aims to offer a user-friendly experience with support for both HTTP and gRPC protocols with.

## What Chapar means?
Chapar was the institution of the royal mounted couriers in ancient Persia.
The messengers, called Chapar, alternated in stations a day's ride apart along the Royal Road.
The riders were exclusively in the service of the Great King and the network allowed for messages to be transported from Susa to Sardis (2699 km) in nine days; the journey took ninety days on foot.

Herodus described the Chapar as follows:

> There is nothing in the world that travels faster than these Persian couriers. Neither snow, nor rain, nor heat, nor darkness of night prevents these couriers from completing their designated stages with utmost speed.
>
> Herodotus, about 440 BC

## State of the project
Chapar is currently in the early beta phase, with the first release expected soon. The project is under active development, with regular updates and improvements planned to enhance the user experience and functionality.

### Features
* Create and manage workspaces to organize your API endpoints.
* Create and manage environments to store variables and configurations for your API endpoints.
* Create and manage requests to test your API endpoints.
* Send requests with different methods (GET, POST, PUT, DELETE, PATCH, HEAD, OPTION,CONNECT).
* Send requests with different content types (JSON, XML, Form, Text, HTML).
* Send requests with different authentication methods (Basic, Bearer, API Key, No Auth).
* Send requests with different body types (Form, Raw, Binary).
* Set environment variables from the response of the request using JSONPath.
* Dark mode support.
* Data is stored locally on your machine. and no data is sent to any server.
* Import collections and requests from Postman.

### Roadmap
* Support for gRPC, WebSocket, GraphQL protocol.
* Syntax highlighting for request body.
* Python as a scripting language for pre-request and post-request scripts.
* Support for tunneling to servers and kube clusters as pre request actions.
