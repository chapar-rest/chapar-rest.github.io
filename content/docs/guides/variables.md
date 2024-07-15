---
weight: 2004
title: "Variables"
description: ""
icon: "article"
date: "2024-07-15T01:18:24+01:00"
lastmod: "2024-07-15T01:18:24+01:00"
draft: false

---

Chapar comes with predefined variables that you can use in your requests. These variables can be used to dynamically generate values for headers, parameters, and request bodies. Here are some of the variables that are available in Chapar:

- `{{randomUUID4}}` - Generates a random UUID version 4.
- `{{timeNow}}` - Generates the current time in RFC3339 format.
- `{{unixTimestamp}}` - Generates the current Unix timestamp.
- `{{randInt1000}}` - Generates a random integer between 0 and 1000.
- `{{randInt100}}` - Generates a random integer between 0 and 100.
- `{{randInt}}` - Generates a random integer between 0 and maxInt.
- `{{randFloat}}` - Generates a random 64 float
- `{{randFloat32}}` - Generates a random 32 float
- `{{fullDate}}` - Generates the current date in the format "2006-01-02".
- `{{randBool}}` - Generates a random boolean value.
