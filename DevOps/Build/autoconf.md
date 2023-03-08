---
title: Autoconf
author: Christian Külker
date: 2023-03-08
version: 0.1.0
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Build
commands:
- autoconf
- bootstrap
- make
tags:
- Autoconf
description: Autoconf is an extensible package of M4 macros.

---

Autoconf is a collection of `M4` macros that produce shell scripts to
automatically configure software source code.

## Resources

- <https://www.gnu.org/software/autoconf/>
- <https://git.sv.gnu.org/r/autoconf.git>

## Compiling autoconf from Source

### Dependencies

```bash
aptitude install texinfo help2man
```

## Compiling from Source

Compiling `autoconf` requires `m4` 1.4.8 or later. For Debian 9 stretch this
dependency is fulfilled with `m4` 1.4.18-1.

The README says to read the INSTALL file. However, there is no such file. The
alternative is to read `README-hacking`. And the INSTALL file is created
**after** running `bootstrap`.

```bash
mkdir -p /srv/g/git.sv.gnu.org/r
cd /srv/g/git.sv.gnu.org/r
git clone https://git.sv.gnu.org/r/autoconf.git
Cloning into 'autoconf'...
warning: redirecting to https://git.savannah.gnu.org/r/autoconf.git/
```

Using `bootstrap` will use the repositories `autoconf` to configure `autoconf`.
Alternatively, an `autoconf` installed from a package can do the same.

```bash
cd autoconf
./bootstrap
configure.ac:37: installing 'build-aux/missing'
Makefile.am: installing './INSTALL'
doc/local.mk:23: installing 'build-aux/mdate-sh'
Makefile.am:137:   'doc/local.mk' included from here

export PERL=/usr/bin/perl;
./configure --prefix=/opt
checking for a BSD-compatible install... /usr/bin/install -c
checking whether build environment is sane... yes
checking for a race-free mkdir -p... /bin/mkdir -p
checking for gawk... gawk
checking whether make sets $(MAKE)... yes
checking whether make supports nested variables... yes
checking build system type... x86_64-pc-linux-gnu
checking host system type... x86_64-pc-linux-gnu
configure: autobuild project... GNU Autoconf
configure: autobuild revision... 2.72a.88-beb6
configure: autobuild hostname... s1
configure: autobuild timestamp... 20230308T150945Z
checking for a shell whose -n mode is known to work... /bin/bash
checking for characters that cannot appear in file names... none
checking whether directories can have trailing spaces... yes
checking for expr... /usr/bin/expr
checking for GNU M4 that supports accurate traces... /usr/bin/m4
checking whether /usr/bin/m4 accepts --gnu... yes
checking how m4 supports trace files... --debugfile
checking for Perl >=5.10.0 with Time::HiRes::stat... /usr/bin/perl
checking whether /usr/bin/perl Fcntl::flock is implemented... yes
checking for emacs... emacs
checking whether emacs is sufficiently recent... yes
checking for emacs... emacs
checking where .elc files should go... ${datadir}/emacs/site-lisp
checking for grep that handles long lines and -e... /bin/grep
checking for egrep... /bin/grep -E
checking for a sed that does not truncate output... /bin/sed
checking whether make is case sensitive... yes
checking that generated files are newer than configure... done
configure: creating ./config.status
config.status: creating tests/atlocal
config.status: creating Makefile
config.status: creating lib/version.m4
config.status: executing tests/atconfig commands

You are about to use an experimental version of Autoconf.  Be sure to
read the relevant mailing lists, most importantly <autoconf@gnu.org>.

Below you will find information on the status of this version of Autoconf.

* Status

                 /*------------------------------------.
                 | New features need further testing.  |
                 `------------------------------------*/

* Autotest parallel testsuite support is still a work in progress.
  There are known issues in job handling under dash and some ksh
  variants, where a parallel testsuite can hang or cause a 100%
  processor load.  In the current release, the overall autoconf
  testsuite only exercises parallel autotest under bash and zsh, or if
  you have defined TEST_PARALLEL_AUTOTEST in the environment.
  Meanwhile, Cygwin has known problems with named fifos that cause
  failures when attempting parallel tests in an autotest suite.  It is
  possible that other difficulties will be encountered, whether with
  shell or platform limitations; help is appreciated in improving
  parallel testsuite support.
```

```bash
make
/bin/mkdir -p stdlib nasmlib output asm disasm x86 common macros test doc nsis
make nasm ndisasm
make[1]: Entering directory '/srv/g/github.com/netwide-assembler/nasm'
gcc -c -std=c11 -g -O2 -fwrapv -U__STRICT_ANSI__ -fno-common [...]
[...]
sm -I./output -I./output -Wl,--gc-sections -o ndisasm disasm/ndisasm.o libnasm.a
make[1]: Leaving directory '/srv/g/github.com/netwide-assembler/nasm
```

```bash
make check
if test -d ./.git                               \
        && git --version >/dev/null 2>&1; then                  \
  cd . &&                                               \
  git submodule --quiet foreach                                 \
      'test "$(git rev-parse "$sha1")"                  \
          = "$(git merge-base origin "$sha1")"'         \
    || { echo 'maint.mk: found non-public submodule commit' >&2;        \
         exit 1; };                                             \
else                                                            \
  : ;                                                           \
fi
make  check-am
make[1]: Entering directory '/tmp/autoconf'
make  check-local
make[2]: Entering directory '/tmp/autoconf'
:;{ \
  echo '# Signature of the current package.' && \
  echo 'm4_define([AT_PACKAGE_NAME],      [GNU Autoconf])' && \
  echo 'm4_define([AT_PACKAGE_TARNAME],   [autoconf])' && \
  echo 'm4_define([AT_PACKAGE_VERSION],   [2.72a.88-beb6])' && \
  echo 'm4_define([AT_PACKAGE_STRING],    [GNU Autoconf 2.72a.88-beb6])' && \
  echo 'm4_define([AT_PACKAGE_BUGREPORT], [bug-autoconf@gnu.org])' && \
  echo 'm4_define([AT_PACKAGE_URL],       [https://www.gnu.org/software/autoconf/])'; \
} > tests/package.m4-t
mv tests/package.m4-t tests/package.m4
/usr/bin/perl ./tests/mktests.pl tests ./lib/autoconf/general.m4 \
./lib/autoconf/status.m4 ./lib/autoconf/autoheader.m4 \
./lib/autoconf/autoupdate.m4 ./lib/autoconf/specific.m4 \
./lib/autoconf/functions.m4 ./lib/autoconf/lang.m4 ./lib/autoconf/c.m4 \
./lib/autoconf/erlang.m4 ./lib/autoconf/fortran.m4 ./lib/autoconf/go.m4 \
./lib/autoconf/headers.m4 ./lib/autoconf/libs.m4 ./lib/autoconf/types.m4 \
./lib/autoconf/programs.m4
autom4te_perllibdir='.'/lib AUTOM4TE_CFG='lib/autom4te.cfg'        \
 bin/autom4te -B ''lib -B '.'/lib         --language=autotest -I tests \
-I ./tests suite.at -o tests/testsuite.tmp
mv tests/testsuite.tmp tests/testsuite
/bin/bash tests/testsuite -C tests MAKE=make
## -------------------------------------- ##
## GNU Autoconf 2.72a.88-beb6 test suite. ##
## -------------------------------------- ##

Executables (autoheader, autoupdate...).

  1: Syntax of the shell scripts                     ok
  2: Syntax of the Perl scripts                      ok
  3: autom4te cache                                  ok
[...]
602: AC_FUNC_VPRINTF                                 ok

Compatibility with external tools and macros.

603: Libtool                                         ok
604: shtool                                          skipped (foreign.at:130)
605: AX_PROG_CC_FOR_BUILD                            ok
606: AX_PROG_CXX_FOR_BUILD                           ok
607: gnulib-std-gnu11.m4                             ok

Autoscan.

608: autoscan                                        ok

## ------------- ##
## Test results. ##
## ------------- ##

566 tests behaved as expected.
42 tests were skipped.
make[2]: Leaving directory '/tmp/autoconf'
make[1]: Leaving directory '/tmp/autoconf'
```

```bash
make install

make  install-am
make[1]: Entering directory '/srv/g/git.sv.gnu.org/r/autoconf'
make  install-exec-am install-data-am
make[2]: Entering directory '/srv/g/git.sv.gnu.org/r/autoconf'
:
list='bin/autoconf bin/autoheader bin/autom4te bin/autoreconf bin/autoscan \
bin/autoupdate bin/ifnames'; test -n "/usr/local/bin" || list=; \
if test -n "$list"; then \
  echo " /bin/mkdir -p '/usr/local/bin'"; \
  /bin/mkdir -p "/usr/local/bin" || exit 1; \
fi; \
for p in $list; do \
  if test -f "$p"; then d=; else d="./"; fi; \
  if test -f "$d$p"; then echo "$d$p"; echo "$p"; else :; fi; \
done | \

[...]
make  install-data-hook
make[3]: Entering directory '/srv/g/git.sv.gnu.org/r/autoconf'
for s in build-aux/config.guess build-aux/config.sub build-aux/install-sh; do \
  chmod +x "/usr/local/share/autoconf/$s"; \
done
make[3]: Leaving directory '/srv/g/git.sv.gnu.org/r/autoconf'
make[2]: Leaving directory '/srv/g/git.sv.gnu.org/r/autoconf'
make[1]: Leaving directory '/srv/g/git.sv.gnu.org/r/autoconf'
```

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2023-03-08 | Initial release                                      |


