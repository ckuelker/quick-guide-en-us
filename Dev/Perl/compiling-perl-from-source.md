---
title: Compiling Perl From Source
type: doc
author: Christian Külker
version: 0.1.2
date: 2023-03-21
disclaimer: True
categories:
 - Perl
tags:
 - Tar
 - Environment
 - Debian 10 Buster
description: Compiling Perl from source is easier than most people think.

---

## Introduction

> Compiling Perl from source is easier than most people think.

This document uses Perl 5.20 on Debian Jessie as an example of how to compile a
custom version of Perl. It is to be expected that versions of Perl that are
very different from the distributed versions may not compile.

|  # | Debian   | Perl Version |
| -- | -------- | ------------ |
|  8 | Jessie   | 5.20         |
|  9 | Stretch  | 5.24         |
| 10 | Buster   | 5.28         |
| 11 | Bullseye | 5.32         |


## Compiling Perl From Source

```bash
mkdir -p /srv/perl-5.20.0/src
cd /srv/perl-5.20.0/src
echo http://www.cpan.org/src/5.0/perl-5.20.0.tar.gz > url.txt
wget http://www.cpan.org/src/5.0/perl-5.20.0.tar.gz
mkdir ../build
cd ../build/
tar xvzf perl-5.20.0.tar.gz
cd perl-5.20.0
sh Configure -de -Dprefix='/srv/perl-5.20.0'
 #  -d : use defaults for all answers.
 #  -e : go on without questioning past the production of config.sh.
```

## Configuring a Custom Environment

This section introduces a version-specific Perl environment.

```bash
mkdir -p /srv/env
vim /srv/env/perl-5-20-2
```

File content:

```bash
#` ENV for perl-5.20.2
DIR=/srv/perl-5.20.2
PL=lib/perl5
PV=lib/i486-linux-gnu-thread-multi
export PERL_MB_OPT="--install_base $DIR"
export PERL_MM_OPT="INSTALL_BASE=$DIR"
export PERL5LIB="$DIR/$PL:$DIR/$PV:$PERL5LIB"
export PATH="$DIR/bin:$PATH"
```

```bash
cd /srv/env
ln -s perl-5-20-2 perl
```

## Using The New Custom Perl

To use a custom version of Perl, some shell variables should be set. This is
done using the `source` shell command.

```bash
source /srv/env/perl

perl -v

This is perl 5, version 20, subversion 2 (v5.20.2) built for
x86_64-linux-gnu-thread-multi (with 47 registered patches, see perl -V for
more detail)

Copyright 1987-2015, Larry Wall

Perl may be copied only under the terms of either the Artistic License or
the GNU General Public License, which may be found in the Perl 5 source
kit.

Complete documentation for Perl, including FAQ lists, should be found on
this system using "man perl" or "perldoc perl".  If you have access to the
Internet, point your browser at http://www.perl.org/, the Perl Home Page.
```

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2023-03-21 | Improve writing, add versions of Debian              |
| 0.1.1   | 2022-05-10 | Description, typos, history                          |
| 0.1.0   | 2016-06-27 | Initial release                                      |


