---
title: sc
author: Christian Külker
date: 2024-02-01
version: 0.1.0
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
PDF: True
categories:
- Spread sheet
commands:
- sc
- make
- configure
- wget
tags:
- vim
- sc
description: >
  The program sc (2002) is a ncurses spread sheet application with vim
  key-bindings.

---

## Introduction

sc is a free, cross-platform spreadsheet and calculator tool that works on
Unix, Unix-like, and Windows systems. It's accessible via a terminal emulator,
featuring a simple interface with Vim-like keyboard shortcuts. You can use it
for tasks like financial and budget calculations. Built on the ncurses
interface library, sc has a comprehensive man page, a math formula library, and
supports plugins. It's included in default repositories of popular Linux
distributions like Debian and Ubuntu.

This document covers at least 3 versions:

(a) sc-orig 2002: This is a much modified version of the public domain spread
sheet program sc, originally by James Gosling, and posted a number of years ago
by Mark Weiser as vc, maintained by Chuck Martin [sc 7.16].

[sc 7.16]:
http://www.ibiblio.org/pub/Linux/apps/financial/spreadsheet/sc-7.16.tar.gz

(b) sc-ntroff 2018: The Github fork 7.16 from 2002 made by "n-t-roff" with 6
contributors (2024) published on [github] with a 2018 release of [7.16_1.1.2],
that contains a lot of bug fixes.

[github]: https://github.com/n-t-roff/sc
[7.16_1.1.2]: https://github.com/n-t-roff/sc/releases/tag/7.16_1.1.2

(c) sc-debian 2022: The Debian [package version for bookworm] v[7.16-4.1]
derived from the [2002 version], maintained by [Adam Majer] which still points
to Alioth VCS (2024) and has no link to a sc homepage. Debian stared probably
at around [2014 with 7.16-4] to ship sc.

[package version for bookworm]: https://packages.debian.org/bookworm/sc
[7.16-4.1]: http://deb.debian.org/debian/pool/main/s/sc/sc_7.16-4.1.debian.tar.xz
[Adam Majer]: https://qa.debian.org/developer.php?login=adamm%40zombino.com
[2002 version]: http://deb.debian.org/debian/pool/main/s/sc/sc_7.16.orig.tar.gz
[2014 with 7.16-4]: http://deb.debian.org/debian/pool/main/s/sc/sc_7.16-4.debian.tar.xz

While _sc-ntroff_ and _sc-debian_ derived from _sc-orig_, it is seems (to me)
that there is unfortunately no relation between _sc-ntroff_ and _sc-debian_ and
this is a missed opportunity.

## Build sc-orig 7.16 From Source on Debian 12 Bookworm [0.1.0]

As root

```bash
mkdir -p /srv/{src,build}
chown $USER:$GROUP /srv/{src,build}
```

As $USER

```bash
cd /srv/src
wget http://www.ibiblio.org/pub/Linux/apps/financial/spreadsheet/sc-7.16.tar.gz
cd /srv/build
tar xvzf ../src/sc-7.16.tar.gz
cd sc-7.16
# You might read and change `Makefile` for your needs. Especially if you
# do not use Linux
make
gcc -DSYSV3 -O2 -pipe -DDFLT_PAGER=\"less\" -c abbrev.c
abbrev.c: In function ‘add_abbr’:
[...]
  645 |     (void) exit(0);
      |            ^~~~
qhelp.c:645:12: note: include ‘<stdlib.h>’ or provide a declaration of ‘exit’
```

You can find the binary `sc` inside the current directory

## Build sc-ntroff 7.16_1.1.2 From Source on Debian 12 Bookworm [0.1.0]

As root

```bash
mkdir -p /srv/{src,build}
chown $USER:$GROUP /srv/{src,build}
```

As $USER

```bash
cd /srv/src
wget https://github.com/n-t-roff/sc/archive/refs/tags/7.16_1.1.2.tar.gz
tar xvzf ../src/7.16_1.1.2.tar.gz
cd sc-7.16_1.1.2
./configure
Checking for make(1) ... yes
Checking for -ffloat-store ... yes
Checking for strlcpy(3) ... no
Checking for strlcat(3) ... no
Checking for -lncursesw ... yes (#include <ncursesw/curses.h>,\
 -I/usr/include/ncurses6, -L/usr/lib64/ncurses6)
Checking for wins_wch(3) ... yes
Checking for keyname(3) ... yes
Checking for isfinite(3) ... yes
Checking for sc(1) attr_get(3) ... yes
make
cc  -I/usr/include/ncurses6 -DHAVE_ATTR_T -DHAVE_ATTR_GET -DHAVE_FIXTERM\
 -DHAVE_RESETTERM -DHAVE_NCURSESW_CURSES_H -DHAVE_CURSES_WCH\
 -DHAVE_CURSES_KEYNAME -DHAVE_ISFINITE -DHAVE_STDBOOL_H\
 -DDFLT_PAGER=\"less\" -c abbrev.c
Checking for <stdbool.h> ... yes
[...]
name=sc NAME=SC LIBDIR=/usr/local/share/sc sh torev sc.doc > sc.1
name=sc NAME=SC LIBDIR=/usr/local/share/sc sh torev psc.doc > psc.1
name=sc NAME=SC LIBDIR=/usr/local/share/sc sh torev scqref.doc > scqref.1
name=sc sh torev < xsc.sh > xsc
name=sc NAME=SC LIBDIR=/usr/local/share/sc sh torev xsc.doc > xsc.1
```

You can find the binary `sc` inside the current directory

## Install sc-debian 7.16-4.1 From Package on Debian 12 Bookworm [0.1.0]

The package [sc] depends on:

- libc6 (>= 2.33)
- libncurses6 (>= 6)
- libtinfo6 (>= 6)

```bash
aptitude install sc
```

This will install `7.16-4.1`.

| Debian |           | Version               |
| ------ | --------- | --------------------- |
|        | Sid       | 7.16-4.1+b1           |
|        | Trixie    | 7.16-4.1              |
| 12     | Bookwowrm | 7.16-4.1              |
| 11     | Bullseye  | 7.16-4+b3             |
| 10     | Buster    | 7.16-4+b3             |

## Build sc-debian 7.16-4.1 Package on Debian 12 Bookwork [0.1.0]

As root

```bash
mkdir -p /srv/deb/pkg-sc-7.16-4.1
chown  $USER:$GROUP /srv/deb/pkg-sc-7.16-4.1
aptitude install build-essential debhelper devscripts
```

As User

```bash
cd /srv/deb/pkg-sc-7.16-4.1
wget http://deb.debian.org/debian/pool/main/s/sc/sc_7.16.orig.tar.gz
wget http://deb.debian.org/debian/pool/main/s/sc/sc_7.16-4.1.debian.tar.xz
tar xvzf sc_7.16.orig.tar.gz
cd sc-7.16
tar xvJf ../sc_7.16-4.1.debian.tar.xz
dpkg-buildpackage -us -uc
dpkg-buildpackage: info: source package sc
dpkg-buildpackage: info: source version 7.16-4.1
dpkg-buildpackage: info: source distribution unstable
dpkg-buildpackage: info: source changed by Ying-Chun Liu (PaulLiu)\
 <paulliu@debian.org>
dpkg-buildpackage: info: host architecture amd64
 dpkg-source --before-build .
dpkg-source: info: using patch list from debian/patches/series
dpkg-source: info: applying use_varadic_macro
dpkg-source: info: applying call_function_not_take_its_address
dpkg-source: info: applying Upstream-changes-from-old-versions
dpkg-source: info: applying function_definitions
dpkg-source: info: applying nonotimeout.patch
dpkg-source: info: applying hardening
 fakeroot debian/rules clean
dh clean
   debian/rules override_dh_auto_clean
make[1]: Entering directory '/srv/deb/pkg-sc-7.16-4.1/sc-7.16'
dh_auto_clean
        make -j4 distclean
make[2]: Entering directory '/srv/deb/pkg-sc-7.16-4.1/sc-7.16'
rm -f *.o *res.h y.tab.h sc psc debug core gram.c \
sc.1 sc.man psc.man psc.1 y.output scqref \
pxmalloc.c pvmtbl.c qhelp.c tags
make[2]: Leaving directory '/srv/deb/pkg-sc-7.16-4.1/sc-7.16'
find . -name '*~' -exec rm {} \;
make[1]: Leaving directory '/srv/deb/pkg-sc-7.16-4.1/sc-7.16'
   dh_clean
 dpkg-source -b .
dpkg-source: info: using source format '3.0 (quilt)'
dpkg-source: info: building sc using existing ./sc_7.16.orig.tar.gz
dpkg-source: info: using patch list from debian/patches/series
dpkg-source: info: building sc in sc_7.16-4.1.debian.tar.xz
dpkg-source: info: building sc in sc_7.16-4.1.dsc
 debian/rules build
dh build
   dh_update_autotools_config
   dh_autoreconf
   dh_auto_configure
   debian/rules override_dh_auto_build
make[1]: Entering directory '/srv/deb/pkg-sc-7.16-4.1/sc-7.16'
dh_auto_build -- all sc.1 psc.1 CC=gcc CFLAGS="-Wall -DSYSV3 -ffloat-store -g\
 -O2 -ffile-prefix-map=/srv/deb/pkg-sc-7.16-4.1/sc-7.16=.\
 -fstack-protector-strong -Wformat -Werror=format-security"
        make -j4 "INSTALL=install --strip-program=true" all sc.1 psc.1 CC=gcc\
 "CFLAGS=-Wall -DSYSV3 -ffloat-store -g -O2\
 -ffile-prefix-map=/srv/deb/pkg-sc-7.16-4.1/sc-7.16=. -fstack-protector-strong\
 -Wformat -Werror=format-security"
make[2]: Entering directory '/srv/deb/pkg-sc-7.16-4.1/sc-7.16'
[...]
In file included from cmds.c:31:
cmds.c: In function ‘doend’:
cmds.c:1394:16: warning: zero-length gnu_printf format string\
 [-Wformat-zero-length]
 1394 |         error ("");     /* clear line */
      |
[...]
# dh_auto_install
make[1]: Leaving directory '/srv/deb/pkg-sc-7.16-4.1/sc-7.16'
   dh_install
   dh_installdocs
   dh_installchangelogs
   dh_installman
   dh_perl
   dh_link
   dh_strip_nondeterminism
   dh_compress
   dh_fixperms
   dh_missing
   dh_strip
   dh_makeshlibs
   dh_shlibdeps
   dh_installdeb
   dh_gencontrol
   dh_md5sums
   dh_builddeb
dpkg-deb: building package 'sc' in '../sc_7.16-4.1_amd64.deb'.
dpkg-deb: building package 'sc-dbgsym' in '../sc-dbgsym_7.16-4.1_amd64.deb'.
 dpkg-genbuildinfo -O../sc_7.16-4.1_amd64.buildinfo
 dpkg-genchanges -O../sc_7.16-4.1_amd64.changes
dpkg-genchanges: info: not including original source code in upload
 dpkg-source --after-build .
dpkg-source: info: unapplying hardening
dpkg-source: info: unapplying nonotimeout.patch
dpkg-source: info: unapplying function_definitions
dpkg-source: info: unapplying Upstream-changes-from-old-versions
dpkg-source: info: unapplying call_function_not_take_its_address
dpkg-source: info: unapplying use_varadic_macro
dpkg-buildpackage: info: binary and diff upload (original source NOT included)
```

As it can be seen by many warnings (mostly omitted in the above output), this is
similar to _sc-orig_ from 2002 and not the fixed version _sc-ntroff_ from 2018.
The package can be found `../sc_7.16-4.1_amd64.deb`. Also a package
`../sc-dbgsym_7.16-4.1_amd64.deb` can be found.

## Further Readings

- [sc on wikipedia]
- [sc on github]

[sc on wikipedia]: https://en.wikipedia.org/wiki/Sc_(spreadsheet_calculator)
[sc on github]: https://github.com/n-t-roff/sc

## Related Projects

- [sc-im]
- [Spread sheet programs]

[sc-im]: https://github.com/andmarti1424/sc-im
[Spread sheet programs]:
  http://www.ibiblio.org/pub/Linux/apps/financial/spreadsheet/!INDEX.html

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2024-02-01 | Initial release                                      |

