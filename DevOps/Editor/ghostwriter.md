---
title: Ghostwriter
linkTitle: ghostwriter
author: Christian Külker
version: 0.1.0
date: 2025-07-28
type: doc
locale: en_US
lang: en
disclaimer: True
toc: True
categories:
- Tools
commands:
- ghostwriter
tags:
- ghostwriter
discription: Distrcation free Markdown editor

---

## Introduction

The package description claims that ghostwriter is a "Distraction-free,
themeable Markdown editor [...]" and this not as spot on per default compared
to the apostrophe editor, but the ghostwriter editor view contains minimal
distractions with some additional configuration. The application provides
a menu, sidebar, and preview pane in its default configuration.

## Other

- [Apostrophe](apostrophe.md)

## Links

- Home <https://ghostwriter.kde.org/>

## Debian

| Debian      | Version      | Remark                   |
| ----------- | ------------ | ------------------------ |
| 11 Bullseye | 1.8.1-2      |                          |
| 12 Bookworm | 2.1.6+ds-2   |                          |
| 13 Trixie   | 25.04.0+ds-1 | testing as of 2025-07-30 |

## Critique

### Pros

- White background can be enabled (via "Click the moon")
- Markdown flavor for preview can be changed
- Pandoc YAML front matter is correctly detected
- Spell checking is available
- Supports theming
- Font can be configured
- Font settings for editor and preview are independent
- Auto-save functionality is present
- Tab stops are configurable; spaces can be inserted instead
- Manual reload ("Reload from Disk ...") is available
- Distraction-free mode removes the left sidebar; however, it does not remove
  the top and bottom menu bars. Full-screen mode additionally removes the top
  menu bar, leaving only the bottom, and thus qualifies as a distraction-free
  mode.
- Editor-only mode is supported
- Provides readability metrics ("reading ease" and "grade level")
- Supports export to other formats

### Cons

- Default background is dark
- Only one theme is included by default
- Editor and preview panes are not synchronized during scrolling
- No automatic reload from disk
- Manual reload lacks a hotkey, reducing usability with external editors
- Preview cannot be used independently (editor is always visible)
- Default theme renders preview less aesthetically than Apostrophe; not clear
  if this can be improved
- No syntax highlighting in preview
- Custom themes appear to allow color adjustments only

## History

| Version | Date       | Notes                                               |
| ------- | ---------- | --------------------------------------------------- |
| 0.1.0   | 2025-07-28 | Initial release                                     |
