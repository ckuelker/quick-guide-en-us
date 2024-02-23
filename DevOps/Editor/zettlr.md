---
title: Zettlr
author: Christian Külker
version: 0.1.1
date: 2024-02-23
type: doc
locale: en_US
lang: en
disclaimer: True
toc: True
categories:
- Editor
commands:
- zettlr
tags:
- zettlr
discription: Zettlr brings simplicity back to your texts

---

## Introduction

Zettlr, an open-source Markdown editor, is increasingly popular among
researchers, students, writers, and knowledge workers for its simplicity and
advanced functionality. It uses Markdown, a lightweight markup language that
emphasizes content over formatting, offering a distraction-free writing
experience. This is especially useful where ease of use and cross-platform
compatibility are crucial. Additionally, Zettlr integrates with reference
management tools like Zotero (https://www.zotero.org/) and BibTeX
(https://docs.zettlr.com/en/core/citations/), streamlining the management of
bibliographies and citations in academic writing and research.

## Debian 12 Bookworm Zettlr 3.0.5

This section covers the installation of Zettlr as unofficial Debian packages
from the GitHub repository release site, as there is currently no official
Debian package.

- GitHub development source code: <https://github.com/Zettlr/Zettlr>
- GitHub stable source code: <https://github.com/Zettlr/Zettlr/tree/master>
- GitHub releases: <https://github.com/Zettlr/Zettlr/releases>

### Installation

Install Zettlr 3.0.5 on Debian 12 Bookworm using these commands:

```bash
cd /srv/src
export V=3.0.5
export A=amd64
wget https://github.com/Zettlr/Zettlr/releases/download/v$V/Zettlr-${V}-$A.deb
apt-get install -f ./Zettlr-${V}-$A.deb
```

This installation includes `gir1.2-gnomekeyring-1.0` and `libgnome-keyring0`
for the _amd64_ architecture. Suggested packages like `quarto` and `tex-live`
are not installed by default. Quarto, an open-source scientific and technical
publishing system based on Pandoc, supports dynamic content creation with
Python, R, Julia, and Observable.

For ARM architecture, dependencies include:

- libgtk-3-0:arm64
- libnotify4:arm64
- libnss3:arm64
- libxtst6:arm64
- libatspi2.0-0:arm64
- libdrm2:arm64
- libgbm1:arm64
- libxcb-dri3-0:arm64

### Usage

Zettlr's binary installation location has changed from `/opt/` in version 1.3.0
to `/usr/bin/` in version 3.0.5. To launch Zettlr, simply use:

```bash
zettlr
```

On startup, Zettlr creates necessary directories and files under
`$HOME/.config/Zettlr`.  The application logs startup activities and
environment checks.

Note: Zettlr does not have a manual page, and its command-line help is minimal:

```bash
zettlr --help
```

While Zettlr supports opening individual files, it is optimized for operating
with directories.

### Discussion

1. __Markdown Rendering Issues__: Zettlr has difficulties with certain GitHub
flavored Markdown syntax, such as displaying single Markdown pages verbatim.
For instance, a text like `DevOps/Tools/fzf.md,` is incorrectly rendered as a
hyperlink.

  ```markdown
  DevOps/Tools/
  [fzf.md](fzf.md)
  ,
  ```
2. __Unable to Open a Work Directory on Commandline__:

Opening a directory like `zettlr quick-guide-en-us` has not the desired effect.
It will open a blank window, even though the Zettlr manual claims that the
default modus operandi for Zettlr should be directories not files.  Of course
under "File" -> "Open Workspace" a directory can be opened and used, but for
people used to work on the command line, Zettlr is not intuitive.


3. __Lint Tool Conflicts__: Zettlr's lint tool, `remark-lint`, conflicts with
Pandoc's handling of 'shortcut_reference_links'. Zettlr complains about these
links as _remark-lint (no-shortcut-reference-link)_, while Pandoc processes
them correctly.

```markdown
Pandoc: OK
Zettlr: NG

[text]

[text]: link

Pandoc: OK
Zettlr: OK

[text][]

[text]: link
```

  - [remark-lint](https://unifiedjs.com/explore/package/remark-lint-no-shortcut-reference-link/)
  - [shortcut_reference_links](https://pandoc.org/MANUAL.html#extension-shortcut_reference_links)

    However, even if the linter is silence by adding trailing square brackets
    like this ``[text][]``, then it is still not displayed as a link similar as
    to inline links ``[text](link)`` are displayed.

4. __UI Issues with Preferences Dialog__: The preferences dialog in Zettlr may
have UI issues, such as the inability to close, which could be related to the
MATE desktop environment, as the preference dialog is placed under the main
top panel.

5. __Bundled Pandoc__: Zettlr includes Pandoc, which may be redundant for
Debian users who prefer using system-wide installations of Pandoc.

6. __Command Line Option Handling__: Zettlr does not provide feedback for
unknown command-line options.

## Debian 9 Stretch Zettlr 1.3.0

### Git Installation And Start

To install Zettlr 1.3.0 on Debian 9 Stretch from source:

```bash
mkdir -p ~/g/github.com/Zettlr/
cd ~/g/github.com/Zettlr/
git clone https://github.com/Zettlr/Zettlr.git
cd Zettlr
source /srv/env/node-v12.5.0
npm install
npm install yarn
cd source
npm install
npm install yarn
cd ..
./node_modules/.bin/yarn start
```

This command sequence starts Zettlr, although the layout might be inconsistent.

### Debian Package Installation And Start

Alternatively, install Zettlr 1.3.0 using a Debian package:

```bash
cd /srv/srv/
export V=1.3.0
export A=linux-x64


wget https://github.com/Zettlr/Zettlr/releases/download/v$V/Zettlr-${A}-$V.deb
ssh root
aptitude install libappindicator1
dpkg -i Zettlr-linux-x64-1.3.0.deb
/opt/Zettlr/zettlr
```

### Further Reading

- [Installation Guide](https://docs.zettlr.com/install/#linux-debian-8ubuntu-1204fedora-21-or-newer)

## History

| Version | Date       | Notes                             |
| ------- | ---------- | --------------------------------- |
| 0.1.1   | 2024-02-23 | Debian 12 Bookworm Zettlr 3.0.5   |
| 0.1.0   | 2019-07-15 | Initial release                   |

