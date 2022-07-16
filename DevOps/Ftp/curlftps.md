---
title: Curlftps
linkTitle: Curlftps
author: Christian Külker
date: 2022-06-27
version: 0.1.1
type: doc
disclaimer: True
toc: True
categories:
- Ftp
commands:
- curlftps
- rsync
- lftp
- sftp
- ncftp
- mount
- umount
tags:
- Curlftps
- Ftp
- Rsync
- Rsync Module
- Scriptable
- Partially synchronize FTP site
description:
    The usage and critique of Curlftps in conjunction with rsync that makes
    FTP servers partly accessible

---

## Abstract

The tool [curlftps] can be used to "_mount_" the directory of an FTP server.
Together with other tools like [rsync], one can obtain a fine grained and easy
access via the command line with the possibility of scripting. This document
describes, installation, usage and critique of this approach.

## Introduction

Dealing with FTP servers can be tedious with the `ftp` command. Partly improved
are tools like `ncftp`, [lftp] or `sftp`. Some FTP server offer
__rsync-module__ support and [rsync] can be used to partially download a FTP
side. However the local admin would need to know in advance that a certain
part of the FTP server is of special interest and pre-define a
__rsync-module__. Usually this is not the case for exactly the part one wishes
to download.

In this cases [curlftps] can be used.

## Prerequisites

1. To avoid using [curlftps] one should check if the part of the FTP server is
   already available via a __rsync-module__
2. Partly root rights are needed

## Installation

The installation is straight forward and special prerequisites or dependencies
are not needed.

```bash
aptitude install curlftps
```

## Usage

A configuration is not needed. To recursively copy a part of an FTP server
the following steps can be used.

```bash
mkdir /tmp/mnt
curlftps -o allow_other ftp://myusername:mypassword@ftp.domain.tld /tmp/mnt
mkdir /tmp/dst
cd /tmp/dst
rsync -rzv --inplace --append --progress --stats --timeout=7200 /tmp/mnt/somedir .
sudo umount /tmp/mnt
```

## Critique

1. [curlftps] used together with [rsync] can be inefficient compared to [lftp]
   mirror
2. One is able to script [curlftps].
3. Root rights

## Other Techniques

- [rsync] module ([wikipedia][rsync wikipedia])
- [csync]
- [lftp] mirror ([wikipedia][lftp wikipedia])

## Links

- [curlftps]
- [curlftps source package Debian 10 Buster]

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2022-06-27 | Improve wording, grammar; shell->bash                |
| 0.1.0   | 2021-05-04 | Initial release                                      |

[csync]: https://csync.org/
[lftp]: https://lftp.yar.ru/
[lftp wikipedia]: https://en.wikipedia.org/wiki/Lftp
[rsync]: https://rsync.samba.org/
[rsync wikipedia]: https://en.wikipedia.org/wiki/Rsync
[curlftps]: http://curlftpfs.sourceforge.net/
[curlftps source package debian 10 buster]: https://packages.debian.org/source/buster/curlftpfs

