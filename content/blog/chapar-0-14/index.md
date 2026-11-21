---
title: "Chapar 0.12 to 0.14: Git, a Real Code Editor and a Fresh Coat of Paint"
date: 2026-11-21
authors:
  - name: Mohsen Mirzakhani
    link: https://github.com/mirzakhany
tags: [release]
summary: "0.12 builds Git into Chapar and lets it update itself. 0.13 turns every body and script field into a proper code editor. 0.14 moves to Yoga 0.49: attached tabs, a minimap, drag-and-drop import and clearer selection."
---

Three releases in three weeks. 0.12 is about sharing a space with a team, 0.13 about editing, and 0.14 about how Chapar looks and feels.

<!--more-->

## Git, built in (0.12)

A space kept in a Git repository gets a **Git** page. It lists changes by request, collection and environment name rather than by file, commits the ones you tick, pushes and pulls, and makes branches. When a merge conflicts, Chapar shows the conflicting item field by field instead of YAML conflict markers. **Spaces › Clone** gets a teammate's space in one step.

To make that work, a space now stays clean in git: secret values live encrypted in the space's local state instead of the environment file, and saving an unchanged item leaves its file byte for byte as it was. See [Git](/docs/usingchapar/git).

0.12 also checks for new releases at startup and, on macOS, Windows and Homebrew, installs them for you after checking their signatures. The **Timeline** tab became a waterfall, with every step on one time scale.

## A real code editor (0.13)

Bodies, scripts and responses got multiple cursors, code folding, a find bar with regex and preserve case, line commands, and word and line moves. gRPC requests can raise the 4 MB message limit. Every shortcut is listed in [The Editor](/docs/usingchapar/editor).

## Yoga 0.49 (0.14)

Chapar draws its UI with [Yoga](https://github.com/mirzakhany/yoga), and 0.14 picks up its latest release:

- **Document tabs.** The tab in front joins the request below it. Hover a request's tab to see its method and URL.
- **Window titles.** The window is named after the tab in front, so the Dock and window switchers tell Chapar windows apart.
- **Drag and drop import.** Drop Postman collections, OpenAPI specs or `.proto` files on the Requests page to import them.
- **Minimap.** Turn on **Settings › Editor › Show minimap** for a miniature of the document you can click to scroll.
- **Clearer selection.** Selected rows use an accent tint instead of the grey a pressed row has, focus rings sit outside controls, menus and dialogs float on their own surface, and alerts carry a status icon.
- **Typography.** Sidebar headers are small capitals and there is a medium weight for labels, all of it scaling with your UI font size.
- Text fields take the editor's word and line keys (**⌥←**, **⌥⌫**, **⌘⌫**…).

Get 0.14 from the [releases page](https://github.com/chapar-rest/chapar/releases/tag/v0.14.0); if you have 0.12 or later, Chapar offers the update itself.
