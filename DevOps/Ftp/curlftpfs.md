---
title: Curlftpfs
author: Christian Külker
date: 2023-04-04
version: 0.1.2
type: doc
disclaimer: True
toc: True
categories:
- Ftp
commands:
- curlftpfs
- rsync
- lftp
- sftp
- ncftp
- mount
- umount
tags:
- Curlftpfs
- Ftp
- Rsync
- Rsync Module
- Scriptable
- Partially synchronize FTP site
description:
    The use and criticism of Curlftpfs in conjunction with rsync, which makes
    FTP servers partially accessible.

---

## Abstract

The [curlftpfs] utility can be used to "_mount_" the directory of an FTP server.
Together with other tools like [rsync] you can get a fine-grained and easy
access via the command line with the possibility of scripting. This document
describes installtion, use, and critique this approach.

## Introduction

Dealing with FTP servers can be tedious with the `ftp` command. Partially
improved are tools like `ncftp`, [lftp] or `sftp`. Some FTP servers offer
__rsync-module__ support, and [rsync] can be used to partially download an FTP
site. However, the local admin would have to know in advance that a certain
part of the FTP server is of special interest and predefine a __rsync-module__.
Usually this is not the case for exactly the part you want to download.

In this cases [curlftpfs] can be used.

| Debian | Code       | Version    |
|--------|------------|------------|
|  9     | Stretch    | 0.9.2-9+b1 |
| 10     | Buster     | 0.9.2-9+b1 |
| 11     | Bullseye   | 0.9.2-9+b1 |

## Prerequisites

1. To avoid using [curlftpfs], you should check if the part of the FTP server is
   already available via a __rsync-module__.

2. Partly root rights are needed

## Installation

Installation is straightforward and there are no special requirements or
dependencies.

```bash
aptitude install curlftpfs
```

## Usage

No configuration is required. To recursively copy a portion of an FTP server,
the following steps can be used.

```bash
mkdir /tmp/mnt
curlftpfs -o allow_other ftp://myusername:mypassword@ftp.domain.tld /tmp/mnt
mkdir /tmp/dst
cd /tmp/dst
rsync -rzv --inplace --append --progress --stats --timeout=7200 /tmp/mnt/somedir .
sudo umount /tmp/mnt
```

## Critique

1. Using [curlftpfs] with [rsync] can be inefficient compared to [lftp]
   mirroring.
2. One is able to script [curlftpfs].
3. Root rights

## Other Techniques

- [rsync] module ([wikipedia][rsync wikipedia])
- [csync]
- [lftp] mirror ([wikipedia][lftp wikipedia])

## Links

- [curlftpfs]
- [curlftpfs source package Debian 10 Buster]

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2023-04-04 | Improve writing, fix spelling curlftpfs              |
| 0.1.1   | 2022-06-27 | Improve wording, grammar; shell->bash                |
| 0.1.0   | 2021-05-04 | Initial release                                      |

[csync]: https://csync.org/
[lftp]: https://lftp.yar.ru/
[lftp wikipedia]: https://en.wikipedia.org/wiki/Lftp
[rsync]: https://rsync.samba.org/
[rsync wikipedia]: https://en.wikipedia.org/wiki/Rsync
[curlftpfs]: http://curlftpfs.sourceforge.net/
[curlftpfs source package debian 10 buster]: https://packages.debian.org/source/buster/curlftpfs

