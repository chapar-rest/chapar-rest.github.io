---
weight: 1001
title: "Installation"
description: ""
icon: "article"
date: "2024-02-25T15:18:24+01:00"
lastmod: "2024-02-25T15:18:24+01:00"
draft: false

---

### Installation
To install Chapar, you can download the latest release or build the project from source.

### On macOS
On macOS, you can either download the latest release or install Chapar from Apple's App Store.


<a href="https://apps.apple.com/us/app/chapar-rest/id6673918597?mt=12&itscg=30200&itsct=apps_box_badge&mttnsubad=6673918597" style="display: inline-block;">
<img src="https://toolbox.marketingtools.apple.com/api/v2/badges/download-on-the-app-store/black/en-us?releaseDate=1743379200" alt="Download on the App Store" style="width: 150px; height: 50px; vertical-align: middle; object-fit: contain;" />
</a>
<br/><br/>

### Install From AUR
If you are using Arch Linux, you can install Chapar from the AUR using the following command:

```bash
yay -S chapar-bin
```

Please note that AUR package is maintained by a community contributor. (@Monirzadeh ) may not be up to date with the latest release.

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

