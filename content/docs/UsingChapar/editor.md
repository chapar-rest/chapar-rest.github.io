---
title: "The Editor"
weight: 110
summary: "Multiple cursors, folding, find and replace, line commands, the minimap and every editor shortcut"
---

Request bodies, GraphQL queries, scripts, responses and generated code all open in the same code editor. It colors JSON, XML, HTML, Python and the languages of generated code, completes `{{variables}}`, and, with a [language server](../settings#language-servers) turned on, adds completion, hover docs and diagnostics.

The shortcuts below are for macOS. On Windows and Linux use **Ctrl** in place of **⌘**; where the keys differ, the table says so.

## Multiple cursors

| Action | macOS | Windows / Linux |
|--------|-------|-----------------|
| Add a cursor where you click | **⌥-click** | **Alt-click** |
| Add the next occurrence of the selection | **⌘D** | **Ctrl+D** |
| Select every occurrence | **⌘⇧L** | **Ctrl+Shift+L** |
| Add a cursor above / below | **⌘⌥↑** / **⌘⌥↓** | **Ctrl+Alt+↑** / **Ctrl+Alt+↓** |
| Select a column | **⌥⇧-drag** | **Alt+Shift-drag** |
| Back to one cursor | **Esc** | **Esc** |

Typing, deleting, cut, copy and paste act on every cursor, and **Undo** brings back every cursor an edit had.

## Folding

Fold a block from the chevron in the gutter next to its first line. Blocks are found by brackets in JSON and code, and by indentation elsewhere. A folded block shows as `{ … }`.

| Action | macOS | Windows / Linux |
|--------|-------|-----------------|
| Fold / unfold at the caret | **⌥⌘[** / **⌥⌘]** | **Ctrl+Shift+[** / **Ctrl+Shift+]** |
| Fold everything | **⌘K ⌘0** | **Ctrl+K Ctrl+0** |
| Unfold everything | **⌘K ⌘J** | **Ctrl+K Ctrl+J** |

## Find and replace

**⌘F** opens the find bar, **⌥⌘F** (**Ctrl+H**) opens it with replace. It counts matches (*3 of 12*) and has toggles for:

| Toggle | macOS | Windows / Linux |
|--------|-------|-----------------|
| Match case | **⌥⌘C** | **Alt+C** |
| Whole word | **⌥⌘W** | **Alt+W** |
| Regular expression (with `$1` groups in the replacement) | **⌥⌘R** | **Alt+R** |
| Find in selection | **⌥⌘L** | **Alt+L** |
| Preserve case when replacing | **⌥⌘P** | **Alt+P** |

**Enter** / **⇧Enter** go to the next or previous match (also **⌘G** / **⇧⌘G**, or **F3** / **Shift+F3**). In the replace field, **Enter** replaces one match and **⌘Enter** replaces all of them as one undo step. **Esc** closes the bar.

## Lines

| Action | macOS | Windows / Linux |
|--------|-------|-----------------|
| Duplicate lines down / up | **⌥⇧↓** / **⌥⇧↑** | **Alt+Shift+↓** / **Alt+Shift+↑** |
| Move lines down / up | **⌥↓** / **⌥↑** | **Alt+↓** / **Alt+↑** |
| Delete lines | **⌘⇧K** | **Ctrl+Shift+K** |
| Toggle a line comment | **⌘/** | **Ctrl+/** |
| Toggle soft wrap | **⌥⌘W** | **Ctrl+Alt+W** |

## Moving around

| Action | macOS | Windows / Linux |
|--------|-------|-----------------|
| Word left / right | **⌥←** / **⌥→** | **Ctrl+←** / **Ctrl+→** |
| Line start / end | **⌘←** / **⌘→**, **Home** / **End** | **Home** / **End** |
| Start / end of the file | **⌘↑** / **⌘↓** | **Ctrl+Home** / **Ctrl+End** |
| Delete a word left / right | **⌥⌫** / **⌥⌦** | **Ctrl+Backspace** / **Ctrl+Delete** |
| Delete to the line start | **⌘⌫** | |
| Extend the selection | add **⇧** to any move, or **⇧-click** | |

**Home** switches between the first non-blank character and column 0. Text fields such as the URL bar take the same word and line keys.

## Minimap

Turn on **Settings › Editor › Show minimap** to draw a miniature of the document at the editor's right edge, a bar per line in its syntax colors with the lines on screen shaded. Click or drag it to scroll. It hides in editors narrower than 480 px and never shows in notes and descriptions.

## Other editor settings

Font, size, indentation, tab width, auto-closing brackets and quotes, line numbers, line wrapping and the size past which documents are shown without colors are all in [Settings › Editor](../settings#editor).
