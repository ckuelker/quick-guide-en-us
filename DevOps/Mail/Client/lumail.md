---
title: Lumail
linkTitle: Lumail
author: Christian Külker
date: 2023-07-26
version: 0.1.2
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Client
commands:
- ls
tags:
- Lumail
description: Lumail

---

## Introduction

Lumail was recognized as a mail client for Linux up until 2020. Its last update
appears to be in 2020, and as of 2022-07-19, its domain seems to have been
acquired by a business looking to generate traffic. The most recent snapshot of
Lumail can be accessed through the Wayback Machine at [lumail.org]. Both the
[source] code for `lumail` and the website are available at [github.com]. The
last release, lumail 3.1, was in 2017. Regrettably, this project appears to be
dormant, or as the author expressed, "complete" (refer to [#361]).

Lumail's primary functionality revolves around the `Maildir` format, and it
supports IMAP and GPG.

## Critique

### Pros

- Seamless compilation from tar release.
- Straightforward compilation from a git clone.

### Cons

- The Makefile doesn't utilize `./configure` or any other methods for flexible
  compilation.
- The Makefile inadvertently removes global files in `/etc`!!
- Compilation anticipates a git repository and will produce errors
  (specifically, _"fatal: not a git repository (or any of the parent
  directories): .git"_). The implications of this error remain uncertain.

### Recommendation

- Do __NOT__ compile as root.
- Do __NOT__ use clean targets.
- Do __NOT__ use install targets (it will remove files in /etc/luma2).
- Run from a cloned git repository.

## Lumail 3

This section, penned on 2022-07-20, aims to guide users on building and
installing lumail 3.1 from 2017 on Debian 11 Bullseye and Debian 10 Buster.

__In Summary (TL;DR):__ Lumail require `libgmime-2.6-dev` and is incompatible
with Debian 11 Bullseye, which uses `libgmime-3.0-dev`. On Debian 10 Buster,
the compilation process completes successfully, and Lumail can be run from a
cloned git repository after executing `make`.

### Dependencies

```bash
aptitude install  build-essential make pkg-config liblua5.2-dev \
libgmime-3.0-dev libncursesw5-dev libpcre3-dev libmagic-dev
```

### Build

As root

```bash
mkdir -p /srv/{src,build,lumail-3.1}
chown USER.USER /srv/{src,build,lumail-3.1}
```

As user

```bash
cd /srv/src
export URL=https://github.com/lumail/lumail/archive/refs/tags/release-3.1.tar.gz
wget -O lumail-release-3.1.tar.gz $URL
cd ../build
tar xvzf ../src/lumail-release-3.1.tar.gz
```

The `README.md` dropped the warning of eating mail. However a backup is
still reccomended.

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2022-07-26 | Fix layout, typos, style, release to quick guide     |
| 0.1.1   | 2022-07-19 | Update lumail 3.1 and Debian 11 Bullseye             |
| 0.1.0   | 2016-06-22 | Initial release                                      |

[lumail.org]: https://web.archive.org/web/20210128061005/https://lumail.org/).
[github.com]: https://github.com/lumail
[source]: https://github.com/lumail/lumail
[#361]: https://web.archive.org/web/20201109081337/https://github.com/lumail/lumail/issues/361

