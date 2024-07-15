---
weight: 2002
title: "Send GRPC Request"
description: ""
icon: "article"
date: "2024-02-25T15:18:24+01:00"
lastmod: "2024-02-25T15:18:24+01:00"
draft: false

---

To send an GRPC request using Chapar, you can use the following steps:

- Open the Chapar application.
- Click on "New" and select "GRPC Request".
- Enter the URL of the GRPC server.
- In "Server info" select eatheir "Server reflection" or "Proto file" and hit "Reload" to load the server info.
- In "Body" enter the request message in JSON format. you can also use "Load Example" to load an example request.
- Click on "Invoke" to send the request.

You can also add metadata to the request in the "Metadata" section. change request timeout and other settings in the "Settings" section.
Chapar supports both unary and server streaming GRPC requests.

![Send GRPC Request](/images/guides/send_grpc_request.gif)