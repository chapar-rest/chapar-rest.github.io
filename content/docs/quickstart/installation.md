---
title: "Installation"
description: ""
icon: "article"
date: "2024-02-25T15:18:24+01:00"
lastmod: "2024-02-25T15:18:24+01:00"
draft: false

---

### Installation
To install Chapar, you can download the latest release or build the project from source.

#### Download the latest release
You can download the latest release from the [releases page](https://github.com/chapar-rest/chapar/releases).
Be sure to download the version that is compatible with your operating system.

#### Install From Source
To install Chapar from source, clone the repository install the dependencies, and run the application using the following commands:
```bash
git clone https://github.com/chapar-rest/chapar.git
cd chapar
go build -o chapar .
```

## Dependencies
If you want to build the project from source, you need to install the following dependencies:
Chapar is built using [Gio](https://gioui.org) library so you need to install the following dependencies to build the project:

for linux follow instructions in [gio linux](https://gioui.org/doc/install/linux)
for macOS follow instructions in [gio macos](https://gioui.org/doc/install/macos)

