---
title: "Installation"
weight: 100
summary: "How to install Chapar"
---

## Install Release Build

Chapar is available as a release build for macOS, Windows, and Linux. You can download the latest release from the [Chapar GitHub Releases](https://github.com/chapar-rest/chapar/releases) page.

### macOS

On macos you can install Chapar from the [App Store](https://apps.apple.com/us/app/chapar-rest/id6673918597?mt=12&itscg=30200&itsct=apps_box_badge&mttnsubad=6673918597).

### Install from source. 

Chapar is written in Go, so you can install it from source by cloning the repository and running `go build`.

```bash
git clone https://github.com/chapar-rest/chapar.git
cd chapar
go build -o chapar .
```

The GUI framework used is [Gio](https://gioui.org/). and its using the CGO to compile the binary. which is why you need to have the C compiler installed. more details [here](https://gioui.org/doc/install).

### On Arch Linux

On Arch Linux you can install Chapar from the [AUR](https://aur.archlinux.org/packages/chapar-bin). this package is built by the community and is not officially supported by the Chapar team.
