---
title: Compiling Perl From Source
type: doc
author: Christian Külker
date: 2016-06-27
disclaimer: True
categories:
 - Perl
tags:
 - Tar
 - Environment
 - DebianBuster
description: Compiling Perl From Source is easier then most people think

---

## Introduction

> Compiling Perl From Source is easier then most people think.

## Compiling Perl from Source

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

## Configuring A Custom Environment

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


