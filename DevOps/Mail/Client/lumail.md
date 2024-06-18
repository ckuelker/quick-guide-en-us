---
title: Lumail
linkTitle: Lumail
author: Christian Külker
date: 2024-06-18
version: 0.1.3
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
still recommended.

### Critique

__Pros:__

- Seamless compilation from tar release.
- Straightforward compilation from a git clone.

__Cons:__

- The Makefile doesn't utilize `./configure` or any other methods for flexible
  compilation.
- The Makefile inadvertently removes global files in `/etc`!!
- Compilation anticipates a git repository and will produce errors
  (specifically, _"fatal: not a git repository (or any of the parent
  directories): .git"_). The implications of this error remain uncertain.

__Recommendation:__

- Do __NOT__ compile as root.
- Do __NOT__ use clean targets.
- Do __NOT__ use install targets (it will remove files in /etc/luma2).
- Run from a cloned git repository.

## Lumail 2.9

This section has been added for historical reasons. It is not recommended to
use this version.

The old domain is not accessible directly, but indirectly via `archive.org`.

> **NOTE**: `lumail2` may well eat your email, corrupt your email, or
> otherwise cause data loss.  If you have no current backups of your
> email you should **NOT USE THIS PROJECT**.

The latest [lumail2 release 2.9] can be found under the [github] tag
[release-2.9]

```bash
export URL=https://github.com/lumail/lumail/archive/refs/tags/release-2.9.tar.gz
mkdir -p /srv/src
wget -O /srv/src/lumail-release-2.9.tar.gz $URL
cd /srv/build
tar xvzf ../srv/src/lumail-release-2.9.tar.gz
cd lumail-release-2.9
aptitude install  build-essential make pkg-config liblua5.2-dev\
 libgmime-2.6-dev libncursesw5-dev libpcre3-dev libmagic-dev\
 libnet-imap-client-perl libjson-perl
```

[lumail2 release 2.9]: https://github.com/lumail/lumail/archive/refs/tags/release-2.9.tar.gz
[release-2.9]: https://github.com/lumail/lumail/releases/tag/release-2.9

## Lumail 2.5

This section describes the build and install of lumail 2.5 on Debian in 2016
from the upstream tar release. The tar release can be obtained via the
archive.org [lumail.org] link or via a release tag on [github.com].

### Download

```bash
    mkdir src
    cd src
    wget https://lumail.org/download/lumail-2.5.tar.gz
    cd ..
```

__Core:__

```bash
aptitude install  build-essential make pkg-config \
liblua5.2-dev libgmime-2.6-dev  \
libncursesw5-dev libpcre3-dev libmagic-dev
```

__IMAP:__

```bash
aptitude install libnet-imap-client-perl libjson-perl
```

### Build

```bash
mkdir build
cd build
tar xvzf ../src/lumail-2.5.tar.gz
cd umail-2.5
make
```

### Installation

The quick way is to

```bash
make install

# This will do

    cp lumail2 /usr/bin/
    mkdir -p /etc/lumail2/luarocks.d/  || true
    mkdir -p /etc/lumail2/perl.d/  || true
    cp luarocks.d/*.lua /etc/lumail2/luarocks.d/
    cp perl.d/* /etc/lumail2/perl.d/
    rm /etc/lumail2/perl.d/delete-message || true
    rm /etc/lumail2/perl.d/get-folders || true
    rm /etc/lumail2/perl.d/get-messages || true
    rm /etc/lumail2/perl.d/save-message || true
    rm /etc/lumail2/perl.d/set-flags || true
    if [ ! -e /etc/lumail2/lumail2.lua ] ; then
        cp ./lumail2.lua /etc/lumail2/lumail2.lua;
    fi
```

As it can be seen it makes the mistake not to copy the binary to
`/usr/local/bin`

### Configuration and Usage

After global files in `/etc/luamail2` are created the local configuration
should be created:

```bash
mkdir ~/.lumail2/
vim ~/.lumail2/lumail2.lua
```

(description about host specific configuration is omitted)

### IMAP Configuration [DO NOT WORK FOR ME]

By using the documentation for _mail-dir_ or IMAP to generate a configuration
lumail2 was terminating always after some seconds displaying a message list
with the work 'abort'.

It has to be said that the folder I was using was large. 5.3 GB

However using a basically empty folder works.

Used configuration

~~~
Config:set( "global.logfile" , "/home/$USER/.lumail2/lumail.log" )
Log:append( os.date() .. " start lumail 2.5!" )
Config:set( "imap.cache", "/home/$USER/.lumail2/imap.cache" )
Config:set( "imap.proxy", "/etc/lumail2/perl.d/imap-proxy" )
Config:set( "index.sort", "none" )
Config:set( "index.fast", "1" )
Config:set( "imap.server",   "imaps://mail.example.com/" )
Config:set( "imap.username", "$IMAPUSER" )
Config:set( "imap.password", "$PASSWORD" )
~~~

### Basic Configuration [NOT WELL TESTED - PARTLY WORKING]

```bash
mkdir -p /home/$USER/Mail/sent
```

`vim ~/.lumail2/lumail2.lua`

~~~
Config:set( "maildir.prefix",  "/home/$USER/Mail" );
Config:set( "global.sent-mail",  "/home/$USR/Mail/sent" )

-- Set your outgoing mail-handler, and email-address:
Config:set( "global.mailer", "/usr/lib/sendmail -t" )
Config:set( "global.sender", "Some User <test@example.com>" )

-- Set your editor
Config:set( "global.editor", "vim  +/^$ ++1 '+set tw=72'" )
~~~

Looking at mail do work and looks nice

### Critique

__Pros:__

 + easy to compile
 + flexible (not tested)
 + Perl for IMAP (not tested)

__Cons:__

 - Was not able to use 5.3 GB IMAP server
 - Was not able to use 5.3 GB `offlineimap` folder
 - Either has no debug output or was not able
   to activate, even after specifying log file
   in configuration
 - crashed after using vim

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.3   | 2024-06-18 | Readd info 2.5 and 2.9 from 2016 (Critique ...)      |
| 0.1.2   | 2022-07-26 | Fix layout, typos, style, release to quick guide     |
| 0.1.1   | 2022-07-19 | Update lumail 3.1 and Debian 11 Bullseye             |
| 0.1.0   | 2016-06-22 | Initial release                                      |

[lumail.org]: https://web.archive.org/web/20210128061005/https://lumail.org/).
[github.com]: https://github.com/lumail
[source]: https://github.com/lumail/lumail
[#361]: https://web.archive.org/web/20201109081337/https://github.com/lumail/lumail/issues/361

