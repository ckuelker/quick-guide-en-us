---
title: sc-im
author: Christian Külker
date: 2024-02-01
version: 0.1.2
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
PDF: True
categories:
- Spread sheet
commands:
- sc-im
tags:
- sc-im
- vim
- sc
description: >
  The program sc-im (2023) is a ncurses spread sheet application with vim
  key-bindings based on sc (2002).

---

## Introduction

SC-IM, or Spreadsheet Calculator Improvised, is a modern reincarnation of the
classic spreadsheet calculator tool, originally known as SC. This Linux-based
application inherits its foundational design from the original SC, developed by
James Gosling and Mark Weiser, and further enhanced by Chuck Martin. SC-IM
stands out as a vim-like, ncurses-based spreadsheet program, tailored for users
who appreciate the efficiency and keyboard-driven operation typical of the Vim
editor.

## Releases

As of January 31, 2024, the most recent version of SC-IM is v0.8.3, released on
January 12, 2023. This version can be downloaded from
[http://github.com/andmarti1424/sc-im]. As of this writing, there are no
official Debian packages available for this release.

## Build From Source Debian 12 Bookworm [0.1.1]

### Dependencies

Before initiating the build process, ensure that all necessary dependencies are
installed. On Debian 12 Bookworm, the required dependencies can be installed
using the following command:

```bash
# Choose from lua5.4 lua5.3 lua5.2 lua5.1
aptitude install yacc ncurses-dev bison gcc make pkg-config \
                 libxlsxwriter-dev libxml2-dev libzip-dev lua5.4

```

### Build

As root

```bash
mkdir -p /srv/{src,build}
chown $USER:$GROUP /srv/{src,build}
```

As $USER

```bash
cd /srv/src
wget https://github.com/andmarti1424/sc-im/archive/refs/tags/v0.8.3.tar.gz
cd /srv/build
tar xvzf ../src/v0.8.3.tar.gz
cd sc-im-0.8.3
# You might have a look at src/Makefile to match your architecture. For me and
# Debian 12 it seems to match
make -C src
make: Entering directory '/srv/build/sc-im-0.8.3/src'
yacc -d gram.y
mv y.tab.c gram.c
sed -f eres.sed < gram.y > experres.h
sed -f sres.sed < gram.y > statres.h
[...]
make: Leaving directory '/srv/build/sc-im-0.8.3/src
```

You will find `sc-im` in `/srv/build/sc-im-0.8.3/src`.

Optionally you might install it with `make -C src install` (not tested).

## Features

Some major features are:

- Undo, redo
- Markdown export
- CSV import
- ODS import
- Color support
- Wide character support (Japanese, ...)
- GNUPlot interaction

For more features, see the github [repository]

## Sources

- [Repository](https://github.com/andmarti1424/sc-im)

## Debian

There is a [Debian package], but only available in testing (trixie) and
unstable (sid).

| Debian |           | Version               |
| ------ | --------- | --------------------- |
|        | Sid       | 0.8.3+ds-3            |
|        | Trixie    | 0.8.3+ds-3+b1:riscv64 |
| 12     | Bookwowrm | n.a.                  |

[Debian package]: https://packages.debian.org/search?keywords=sc-im
[trixie sc-im]: https://packages.debian.org/trixie/sc-im
[sid sc-im]: https://packages.debian.org/sid/sc-im-dbgsym

## Related Projects

- [sc on Wikipedia]
- [sc on github]
- [Spread sheet programs]

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2024-02-01 | Add Debian section                                   |
| 0.1.1   | 2024-01-31 | Introduction, Build Debian 12                        |
| 0.1.0   | 2022-12-11 | Initial release                                      |

[repository]: https://github.com/andmarti1424/sc-im
[http://github.com/andmarti1424/sc-im]:
  https://github.com/andmarti1424/sc-im/archive/refs/tags/v0.8.3.tar.gz

[sc on wikipedia]: https://en.wikipedia.org/wiki/Sc_(spreadsheet_calculator)
[sc on github]: https://github.com/n-t-roff/sc
[Spread sheet programs]:
  http://www.ibiblio.org/pub/Linux/apps/financial/spreadsheet/!INDEX.html
