---
title: Mail Client Neomutt
linkTitle: Neomutt
author: Christian Külker
date: 2023-07-27
version: 0.1.1
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Mail Client
commands:
- mutt
- neomutt
- ./configure
- make
- tar
- wget
- aptitude
tags:
- Mutt
- NeoMutt
description: Configuration and usage neomutt and sometimes mutt

---

## Introduction

`Neomutt` and `mutt` are renowned command-line clients, also referred to as
**Mail User Agents (MUA)**. They allow users to send and receive emails
directly from the terminal. While traditional local mail is efficiently managed
by these tools, with some configuration, they can also handle IMAP (and POP3).
In the past, I used tools like elm and pine, which had relatively simple user
interfaces; `mutt` and `neomutt` continue in that tradition. My preference for
`neomutt` over `mutt` is primarily due to its superior IMAP handling. That
said, I haven't explored all the alternatives, so there might be better MUAs
available. Feedback is welcome. This guide outlines the challenges faced when
setting up basic mail functionalities in `neomutt` or `mutt`—functionalities
that are typically available out-of-the-box in graphical interfaces.

### IMAP Mail box naming

Surprisingly, renaming one's IMAP folder in the sidebar to something other than
INBOX can be quite challenging. With older versions of `mutt`, it might even be
impossible.

## Dependencies

### Autocrypt

`Autocrypt` requires `sqlite3` as dependency. While the configuration
(`./configure`) works on Debian 9.13 Stretch and 10 Bullseye, the compilation
with `--autocrypt` will stop for Debian 9.13 Stretch with an error.

```
autocrypt/db.c:272:9: warning: implicit declaration of function
    ‘sqlite3_prepare_v3’ [-Wimplicit-function-declaration]
     if (sqlite3_prepare_v3(AutocryptDB,
         ^~~~~~~~~~~~~~~~~~
autocrypt/db.c:281:32: error: ‘SQLITE_PREPARE_PERSISTENT’ undeclared (first use
                            in this function)
                            -1, SQLITE_PREPARE_PERSISTENT, &AccountGetStmt,
                            NULL) != SQLITE_OK)
```

To successfully compile `neomutt` on Debian 9.13 Stretch, exclude the
`--autocrypt` and `--sqlite` options from the `./configure` command.

### gettext

To use `nls`, you need `gettext`. If it's not required, you can disable it with
the `--disable-nls` flag.

```bash
aptitude install gettext
```

### Ncurses

To utilize the `ncurses` user interface, the associated development library
must be installed.

```bash
aptitude install libncurses-dev
```

### libidn

The `stringprep.h` file requires the `libidn11-dev` package.

```bash
aptitude install libidn11-dev
```

### gpgme

```bash
aptitude install libgpgme11-dev
```

### SASL

```bash
aptitude install libsasl2-dev
```

### Krb5

```bash
aptitude install libkrb5-dev
```

### SQLite

`Autocrypt` requires `sqlite`

```bash
aptitude install libsqlite3-dev
```

### GNU TLS

```bash
aptitude install gnutls-dev
```

### Notmuch

```bash
aptitude install notmuch notmuch-mutt libnotmuch-dev
```

### Hcache

While older version of `neomutt` (around 2016) had the configure option
`--hcache` and this option actually shows up in `neomutt -v` it seems to be
that the configure script do not want to know this option any more. However
`hcache` will be used if a back-end is configured. Not all have been tested.

- gdbm
- bdb
- kyotocabinet
- lmdb (tested)
- qdbm
- rocksdb
- tdb
- tokyocabinet

Header compression: (not tested)

- lz4
- zlib
- zstd

For the Berkeley Database (current 5.3) probably do

```bash
aptitude install libdb-dev
```

However, it's not been tested. The tested backend was lmdb, which performed
without issues.

```bash
aptitude install liblmdb-dev
```
## Build

Building `neomutt` follows the conventional process for C programs:
`configure`, `make`, and `make install`. Optional targets (goals) include
`neomutt` and `test`. The compilation is both quick and reliable.

```bash
mkdir -p /srv/build
mkdir  -p /srv/src
cd  /srv/src
export V=20220429
export URL=https://github.com/neomutt/neomutt/archive/refs/tags/$V.tar.gz
wget -O neomutt-$V.tar.gz $URL
cd /srv/build
tar xvzf ../src/neomutt-$V.tar.gz
cd neomutt-$v
./configure --disable-doc --prefix=/srv/neomutt-20220429
```

__Minimal Debian 9.13 Stretch configuration__

~~~
Summary of build options:

  Version:               20220429
  Host OS:               linux-gnu
  Install prefix:        /srv/neomutt-20220429
  Compiler:               cc
  CFlags:                -g -O2 -std=c99 -D_ALL_SOURCE=1 -D_GNU_SOURCE=1 \
                         -D__EXTENSIONS__ -DNCURSES_WIDECHAR \
                         -I/srv/neomutt-20220429/include
  LDFlags:               -L/srv/neomutt-20220429/lib
  Libs:                  -lidn -lncursesw -ltinfo -lanl

  GPGME:                 no
  PGP:                   yes
  SMIME:                 yes
  Notmuch:               no
  Header Cache(s):
  Header Compression(s):
  Lua:                   no
~~~

__Minimal Debian 11 Bullseye configuration___

~~~
Summary of build options:

  Version:               20220429
  Host OS:               linux-gnu
  Install prefix:        /srv/neomutt-20220429
  Compiler:               cc
  CFlags:                -g -O2 -std=c99 -D_ALL_SOURCE=1 -D_GNU_SOURCE=1 \
                         -D__EXTENSIONS__ -DNCURSES_WIDECHAR \
                         -I/srv/neomutt-20220429/include
  LDFlags:               -L/srv/neomutt-20220429/lib
  Libs:                  -lidn -lncursesw -ltinfo -lanl

  GPGME:                 no
  PGP:                   yes
  SMIME:                 yes
  Notmuch:               no
  Header Cache(s):
  Header Compression(s):
  Lua:                   no
~~~

Certain features need to be specified via `./configure` to be able to use them
later. In case you would like to create a `neomutt` configuration similar to an
installed version, you can use `neomutt -v` to show the used `./configure`
options. Some options like `sidebar` are already enabled as default options.

The following `./configure` option have been used for Debian.


~~~
            Stretch Buster Bullseye Remark
            9.13    10     11
--autocypt  no             yes      Enable AutoCrypt support (requires GPGME)
--gnutls    yes            yes      Enable TLS support using GnuTLS
--gpgme     yes            yes      Enable GPGME support
--gss       yes            yes      Use GSSAPI authentication for IMAP
--lmdb      yes            yes      Use LMDB for the header cache
--pgp       yes            yes      PGP support
--prefix    yes            yes      Install path
--sasl      yes            yes      Use the SASL network security library
--smime     yes            yes      SMIME support
--sqlite    no             yes      Enable SQLite support
--mixmaster yes            yes      Enable Mixmaster support
--notmuch   yes            yes      Enable Notmuch support
~~~

```bash
./configure --disable-doc --prefix=/srv/neomutt-20220429 --autocrypt --gnutls\
  --gpgme --gss --pgp --sasl --smime --sqlite --lmdb --mixmaster --notmuch\
  --disable-maintainer-mode
```

When comparing the above configurations to the Debian 11 Bullseye `neomutt`
`./configure` options, it's evident that selecting the right options for
`neomutt` is not straightforward. Kudos to the Debian 11 Bullseye team for
their configuration choices.

```bash
--build=x86_64-linux-gnu --prefix=/usr {--includedir=${prefix}/include}\
 {--mandir=${prefix}/share/man} {--infodir=${prefix}/share/info}\
 --sysconfdir=/etc --localstatedir=/var --disable-option-checking\
 --disable-silent-rules {--libdir=${prefix}/lib/x86_64-linux-gnu}\
 {--libexecdir=${prefix}/lib/x86_64-linux-gnu}\
 --disable-maintainer-mode --disable-dependency-tracking\
 --mandir=/usr/share/man --libexecdir=/usr/libexec --with-mailpath=/var/mail\
 --gpgme --lua --notmuch --with-ui --gnutls --gss --idn --mixmaster --sasl\
 --tokyocabinet --sqlite --autocrypt
```

This are the summaries for the less complex configuration for Debian 9.13
Stretch.

~~~
Summary of build options:

  Version:               20220429
  Host OS:               linux-gnu
  Install prefix:        /srv/neomutt-20220429
  Compiler:               cc
  CFlags:                -g -O2 -std=c99 -D_ALL_SOURCE=1 -D_GNU_SOURCE=1
                         -D__EXTENSIONS__ -I/srv/neomutt-20220429/include
                         -DNCURSES_WIDECHAR -isystem /usr/include/mit-krb5
  LDFlags:               -L/srv/neomutt-20220429/lib
                         -L/usr/lib/x86_64-linux-gnu/mit-krb5 -Wl,-z,relro
                         -lgssapi_krb5 -lkrb5 -lk5crypto -lcom_err
  Libs:                  -lidn -lgnutls -lncursesw -ltinfo -lsqlite3 -lsasl2
                         -lgpgme -lanl  -L/usr/lib/x86_64-linux-gnu -lgpgme
                         -lassuan -lgpg-error -L/usr/lib/x86_64-linux-gnu
                         -lgpg-error
  GPGME:                 yes
  PGP:                   yes
  SMIME:                 yes
  Notmuch:               no
  Header Cache(s):
  Header Compression(s):
  Lua:                   no
~~~


Debian 11 Bullseye

~~~
Summary of build options:

  Version:               20220429
  Host OS:               linux-gnu
  Install prefix:        /srv/neomutt-20220429
  Compiler:               cc
  CFlags:                -g -O2 -std=c99 -D_ALL_SOURCE=1 -D_GNU_SOURCE=1 
                         -D__EXTENSIONS__ -I/srv/neomutt-20220429/include 
                         -DNCURSES_WIDECHAR -isystem /usr/include/mit-krb5
  LDFlags:               -L/srv/neomutt-20220429/lib 
                         -L/usr/lib/x86_64-linux-gnu/mit-krb5 -Wl,-z,relro 
                         -lgssapi_krb5 -lkrb5 -lk5crypto -lcom_err
  Libs:                  -llmdb -lidn -lgnutls -lncursesw -ltinfo -lsqlite3 
                         -lsasl2 -lgpgme -lanl  -L/usr/lib/x86_64-linux-gnu 
                         -lgpgme -lassuan -lgpg-error 
                         -L/usr/lib/x86_64-linux-gnu -lgpg-error
  GPGME:                 yes
  PGP:                   yes
  SMIME:                 yes
  Notmuch:               no
  Header Cache(s):       lmdb
  Header Compression(s):
  Lua:                   no
~~~

## Installation

As is standard, the `make install` command transfers files to the specified
`--prefix` location. For __Debian 9.13 Stretch and 11 Bullseye__, a minimal
configuration will copy the following files. Note: internationalization files
are excluded from this list.

~~~
├── bin
│   └── neomutt
├── etc
│   └── neomuttrc
├── libexec
│   └── neomutt
│       ├── pgpewrap
│       └── smime_keys
└── share
    ├── doc
    │   └── neomutt
    │       ├── colorschemes
    │       │   ├── neonwolf-256.neomuttrc
    │       │   ├── solarized-dark-256.neomuttrc
    │       │   ├── vombatidae.neomuttrc
    │       │   └── zenburn.neomuttrc
    │       ├── hcache-bench
    │       │   ├── neomutt-hcache-bench.sh
    │       │   ├── neomuttrc
    │       │   └── README.md
    │       ├── keybase
    │       │   ├── attachmentView.png
    │       │   ├── decrypt.sh
    │       │   ├── install.sh
    │       │   ├── keybase.py
    │       │   ├── LICENSE
    │       │   ├── neomuttrc
    │       │   ├── pagerMode.png
    │       │   ├── pgpdecrypt.sh
    │       │   ├── pgpverify.sh
    │       │   ├── README.md
    │       │   └── verify.sh
    │       ├── logo
    │       │   ├── neomutt-128.png
    │       │   ├── neomutt-256.png
    │       │   ├── neomutt-32.png
    │       │   ├── neomutt-64.png
    │       │   └── neomutt.svg
    │       ├── lua
    │       │   ├── test_lua-api_runner.neomuttrc
    │       │   └── test_lua-api_spec.lua
    │       ├── oauth2
    │       │   ├── mutt_oauth2.py
    │       │   └── mutt_oauth2.py.README
    │       ├── samples
    │       │   ├── colors.default
    │       │   ├── colors.linux
    │       │   ├── gpg.rc
    │       │   ├── mairix_filter.pl
    │       │   ├── markdown2html.py
    │       │   ├── Mush.rc
    │       │   ├── Pine.rc
    │       │   ├── sample.mailcap
    │       │   ├── sample.neomuttrc
    │       │   ├── sample.neomuttrc-starter
    │       │   ├── sample.neomuttrc-tlr
    │       │   ├── smime_keys_test.pl
    │       │   ├── smime.rc
    │       │   └── Tin.rc
    │       └── vim-keys
    │           ├── README.md
    │           └── vim-keys.rc
    ...
~~~


## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2023-07-27 | Improve style, grammar, release to quick guide       |
| 0.1.0   | 2022-07-18 | Initial release                                      |

