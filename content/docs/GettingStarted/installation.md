---
title: "Installation"
weight: 100
summary: "How to install Chapar on macOS, Windows and Linux, or build it from source"
---

Chapar is released for macOS (Apple Silicon and Intel), Linux (amd64 and arm64) and Windows (amd64). Every release is published on the [GitHub Releases](https://github.com/chapar-rest/chapar/releases/latest) page.

| Platform | File | Notes |
|----------|------|-------|
| macOS, Apple Silicon | `chapar-macos-<version>-arm64.dmg` | Signed and notarized |
| macOS, Intel | `chapar-macos-<version>-amd64.dmg` | Signed and notarized |
| Linux, x86-64 | `chapar-linux-<version>-amd64.tar.xz` | |
| Linux, ARM64 | `chapar-linux-<version>-arm64.tar.xz` | |
| Windows, x86-64 | `chapar-windows-<version>-amd64.zip` | Windows on ARM runs it through emulation |

## macOS

{{< tabs items="Homebrew,DMG,App Store" >}}

{{< tab >}}
Homebrew is the easiest way to install and update Chapar:

```bash
brew tap chapar-rest/chapar
brew install --cask chapar
```

To update to the latest release:

```bash
brew upgrade --cask chapar
```
{{< /tab >}}

{{< tab >}}
Download the DMG for your Mac from the [latest release](https://github.com/chapar-rest/chapar/releases/latest) (`arm64` for Apple Silicon, `amd64` for Intel), open it and drag **Chapar** into **Applications**.
{{< /tab >}}

{{< tab >}}
Chapar is also available on the [Mac App Store](https://apps.apple.com/us/app/chapar-rest/id6673918597?mt=12).

The App Store version runs in a sandbox, so it keeps its data in a different folder. If you already used the downloaded version, copy your data into the sandbox:

```bash
cp -r $HOME/.config/chapar $HOME/Library/Containers/rest.chapar.app/Data/.config
```

or link the two folders so both versions share the same data:

```bash
ln -s $HOME/.config/chapar $HOME/Library/Containers/rest.chapar.app/Data/.config
```
{{< /tab >}}

{{< /tabs >}}

## Windows

Download `chapar-windows-<version>-amd64.zip` from the [latest release](https://github.com/chapar-rest/chapar/releases/latest), extract it and run `chapar.exe`.

{{< callout type="info" >}}
There is no 32-bit Windows build. The GPU library Chapar draws with (wgpu) has no 32-bit Windows version.
{{< /callout >}}

## Linux

Download the `tar.xz` archive for your CPU from the [latest release](https://github.com/chapar-rest/chapar/releases/latest) and extract it:

```bash
tar -xJf chapar-linux-v0.7.0-amd64.tar.xz
./chapar
```

### Arch Linux (AUR)

On Arch-based distributions you can install the [`chapar-bin`](https://aur.archlinux.org/packages/chapar-bin) package with your AUR helper:

```bash
yay -S chapar-bin
```

The AUR package is maintained by a community contributor and may lag behind the latest release.

## Build from source

Chapar is written in Go. Its UI toolkit, [Yoga](https://github.com/mirzakhany/yoga), renders through GLFW and WebGPU, so building needs CGO and a C compiler:

| OS | What to install |
|----|-----------------|
| macOS | Xcode Command Line Tools: `xcode-select --install` |
| Debian / Ubuntu | `sudo apt install gcc libx11-dev libxrandr-dev libxinerama-dev libxcursor-dev libxi-dev libxxf86vm-dev libgl1-mesa-dev` |
| Windows | A MinGW-w64 GCC on your `PATH`, for example from [MSYS2](https://www.msys2.org/) |

Then clone and build:

```bash
git clone https://github.com/chapar-rest/chapar.git
cd chapar
go build -o chapar .
./chapar
```

Dependencies are vendored. If you change them, run `make vendor` instead of `go mod vendor`: the script also copies C sources that `go mod vendor` leaves out.

To build the same packages the releases ship (DMG, `tar.xz`, `zip`), install the Yoga CLI with `make install_deps` and run `yoga package`:

```bash
yoga package -os darwin -arch arm64 -version v0.7.0   # dist/darwin/*.dmg
yoga package -os linux -version v0.7.0                # dist/linux/*.tar.xz
yoga package -os windows -version v0.7.0              # dist/windows/*.zip
```

## Next steps

- [Quick Start](../quick-start): send your first requests to the free mock server.
- [Python Scripting](../../scripting): turn on scripting if you want pre and post-request scripts. It needs Docker.
