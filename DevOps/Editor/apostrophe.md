---
title: Apostrophe
linkTitle: apostrophe
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
- apostrophe
tags:
- apostrophe
discription: Distrcation free Markdown editor

---

## Introduction

The package description "Distraction free Markdown editor" is spot on. The
application has no distracting elements. It shows the document and `CTRL+s`
saves the document and `QTRL-q` exits the application.

## Other

- [Ghostwriter](ghostwriter.md)

## Links

- Home <https://gitlab.gnome.org/World/apostrophe>

## Debian

| Debian         | Version | Remarks    |
| -------------- | ------- | ---------- |
| 11.11 Bullseye | n.a.    | old stable |
| 12.11 Bookworm | 2.6.3-4 | stable     |
| 13 Trixie      | 3.2-3   | testing    |

Dependencies for Debian 13 Trixie (at the time of testing) are among other:

- gir1.2-adw-1
- gir1.2-graphene-1.0
- gir1.2-gtk-4.0
- gir1.2-gtksource-5
- gir1.2-javascriptcoregtk-6.0
- gir1.2-soup-3.0
- gir1.2-spelling-1
- gir1.2-webkit-6.0
- libgtksourceview-5-0
- libgtksourceview-5-common
- libjavascriptcoregtk-6.0-1
- libspelling-1-2
- libspelling-common
- libwebkitgtk-6.0-4
- python3-zombie-telnetlib

## Critique

### Pros

- Distraction free
- It knows more than one Markdown flavour
- It detects pandoc Markdown correctly
- Official Debian package
- Can save Markdown as PDF,HTML, ODT (Libre Office), LaTeX and more
- Can use bigger text
- Other Markdown flavours can be chosen, despite one is auto detected
- Spell checking
- Pretty preview
- The preview can be position in different ways
  - below
  - right
  - as separate window
  - as alternative to editor
 - Scrolling the editor scrolls the preview
 - Scrolling the preview scrolls the editor

### Cons

- Text length of 80 chars seem not supported and line breaks after 79 chars and
  the view looks cluttered
- The grey background of text or code blocks are not straight on the left side
  (when a source code comment is used, as the engine mistakes this for
  a heading)
- No paragraph formatting function (like in vim)
- Can not handle YAML front matter for pandoc (Interprets end of YAML as
  Markdown) in the edit view (It is OK in the preview view)
- The preview can not be used together with an external editor (no auto reload)
- No manually reload for either view
- No close or quit button (while most of the application can be handled with
  a mouse, this can not - not sure this is bad thing, but it feels like
  inconsistency)
- Do not remember window position when killed/closed and reopened (tested under
  Debian 12 MATE) This very annoying for multi monitor setups with blind spots
- No preview at all for Debian 13 (Trixie - testing at the moment of test), see
  below for error and mitigation

__Debian 13 testing (Trixie) Error:__

```bash
Adding pride CSS class flag-disability
src/nv_gbm.c:288: GBM-DRV error (nv_gbm_create_device_native): \
nv_common_gbm_create_device failed (ret=-1)

src/nv_gbm.c:288: GBM-DRV error (nv_gbm_create_device_native): \
nv_common_gbm_create_device failed (ret=-1)

KMS: DRM_IOCTL_MODE_CREATE_DUMB failed: Permission denied
KMS: DRM_IOCTL_MODE_CREATE_DUMB failed: Permission denied
Failed to create GBM buffer of size 1919x1080: Permission denied
```

Tested under Debian 13 (testing - Trixie) MATE and LXQt (Preview was visible
for a fraction of a second)

To make the preview work do

```bash
export WEBKIT_DISABLE_DMABUF_RENDERER=1
```

## History

| Version | Date       | Notes                                               |
| ------- | ---------- | --------------------------------------------------- |
| 0.1.0   | 2025-07-28 | Initial release                                     |


