---
title: Kdiff3
author: Christian Külker
date: 2022-06-20
version: 0.1.2
type: doc
disclaimer: True
TOC: True
categories:
- Diff
tags:
- Kdiff3
- KDE
- diffuse merge tool
- vimdiff
- kompare
- meld
- cmake
- qmake
commands:
- cmake
description: An intuitive diff tool for up to 3 directories or files

---

On the command line slim applications are usually preferred, like the `diff`
command and its friends. However sometimes (human eye) pattern matching is not
very good on the command line and Gnome or other lightweight desktop
environments lack intuitive diff tools. KDE provides `kdiff3` and it serves
well under Gnome and Mate for Debian since a long time until and including
Debian 9.13 Stretch. However `kdiff3` under Debian 10 Buster is useless. I am
not sure if this is just buggy outside KDE or even inside KDE (it probably is).
However `kdiff3` is part of my tool chain and if I am working on Debian 10
Buster, I need to move the task to Debian 9.13 Stretch or in case of
continuation on Debian 10 Buster, I need to move files or whole directory
structures to Debian 9.13 Stretch just for comparison. To circumvent this
inconvenience I tried to find alternatives for `kdiff3` on Debian 10 Buster
under Mate, but did not found a full intuitive replacement so far. The research
included `meld`, `diffuse merge tool`, `vimdiff` and `kompare`. While `meld`
comes close, it is not very intuitive (for me). This document describes some
attempts to compile and use different version of `kdiff3` on different versions
of Debian.

## Versions

| Debian      | kdiff3  | KDE     | Qt     | Window System   | Via     | Usage      |
| ----------- | ------- | ------- | ------ | --------------- | ------- | ---------- |
| 11 Bullseye | 1.8.5-1 | 5.111   |        |                 | package | PASS       |
| 10 Buster   | 1.8     | 5.37+   | 5.8    |                 | source  | PARTIAL OK |
| 10 Buster   | 1.7.90  | 5.54.9  | 5.11.3 | xcb             | package | FAIL       |
| 10 Buster   | 0.9.98  |         | 5      |                 | source  | PASS       |
| 9  Stretch  | 0.9.98  | 4.14.26 |        |                 | package | PASS       |

The above `Window System` is an information provided by the application's about
dialog.

The old version 0.9.98 has its [old home page] on Sourceforge. And it seems the
feature freeze was around 2014-07-04. However there are minor updates and
maintenance done from time to time (updated 3 month ago - as of 2020-12-31).

The new version (Buster) seems to be under active development, and provides a
port to Qt5, but lacks basic functionality (see conclusion later).

With Debian 11 Bullseye `kdiff3-qt` aka `KDiff3` is not developed any more and
replaced by `kdiff3`.

## Kdiff3 for Debian 11 Bullseye

The current package for `kdiff3` provides `kdiff3-1.8.5` and is current 1.5
years behind the latest source release 1.9.5 from 2022-02-23. As this is a
universal tool it might be advised to read the
[handbook](https://docs.kde.org/stable5/en/kdiff3/kdiff3/kdiff3.pdf) to
understand for example how to use `kdiff3` with git.

## Kdiff3 for Debian 10 Buster

### Building kdiff3 0.9.98 On Debian 10 Buster

#### Prerequisites

As prerequisites all packages as in the section `Building KDdiff3 1.8.4 On
Debian 10 Buster` have been installed.

### Compiling

This section introduces two ways of compiling the source. The first will
compile and install the binary to `/usr/local`, the second will modify the
`qmake` project file and install the binary to `/srv/kdiff3-0.9.98`. For both,
due to limitations of the hand made `configure` script and `qmake` a custom
prefix can not be set on the fly via command line.

#### Preparation

For both locations the following has to be set up as root:

```bash
export BUILD_USER=USER_ID
export BUILD_GROUP=GROUP_ID
mkdir -p /srv/{src,source,build,kdiff3-0.9.98}
chown $BUILD_USER:$BUILD_GROUP /srv/{build,source,src,kdiff3-0.9.98}
```

#### /usr/local

The easy way is to use a local user `$BUILD_USER:$BUILD_GROUP` and build the
project for `/usr/local`:

```bash
cd /srv/src
wget https://deac-riga.dl.sourceforge.net/project/kdiff3/kdiff3/0.9.98/\
kdiff3-0.9.98.tar.gz
cd /srv/build
tar xvzf ../src/kdiff3-0.9.98.tar.gz
cd /srv/build/kdiff3-0.9.98
./configure qt4
```

This will build `kdiff3` in `/srv/build/kdiff3-0.9.98/releaseQt/kdiff3`
and it can be used from there. If an installation is needed do as root:

```bash
cd releaseQt
make install
```

#### /srv/kdiff3-0.9.98

The following procedure will compile and install to `/srv/kdiff3-0.9.98`.
Using a source and build directory is not strictly necessary, but helps to
understand what will be changed during the build process.

```bash
cd /srv/src
wget https://deac-riga.dl.sourceforge.net/project/kdiff3/kdiff3/0.9.98/kdiff3-0.9.98.tar.gz
cd /srv/source
tar xvzf ../src/kdiff3-0.9.98.tar.gz
cp -a /srv/source/kdiff3-0.9.98 /srv/build/kdiff3-0.9.98
cd /srv/build/kdiff3-0.9.98
sed -i -e 's%target.path = /usr/local/bin%target.path = \
/srv/kdiff3-0.9.98/bin%' src-QT4/kdiff3.pro
sed -i -e 's%documentation.path = /usr/local/share/doc/kdiff3%\
documentation.path = /srv/kdiff3-0.9.98/doc/kdiff3%' src-QT4/kdiff3.pro
./configure qt4
cd releaseQt
make install
```

### Usage

The binary can be installed in `/srv/kdiff3-0.9.98/bin/kdiff3`. The application
starts also direct from its build location:

```bash
 /srv/build/kdiff3-0.9.98/releaseQt/kdiff3
```

### Building KDdiff3 1.8.4 On Debian 10 Buster

- Supported Qt-versions: 4.8, 5.2 or higher.
- Supported KDE-version: 4, 5

#### Prerequisites

Building `KDiff3` requires the following minimum versions:

- Qt 5.6 or later
- KF5 5.27+
- CMake 3.1
- ECM 1.8
- boost 1.66+

Supported compilers:

- MSVC 2015+
- gcc 5.0+
- clang 3.3+

```bash
aptitude install gettext qtbase5-dev extra-cmake-modules libboost-dev \
libkf5i18n-dev libkf5coreaddons-dev libkf5iconthemes-dev libkf5parts-dev \
libkf5doctools-dev libkf5crash-dev cmake gcc build-essential
```

#### Compiling

This will download, configure and make `kdiff3-1.8.4` and install it
under `/srv/kdiff3-1.8.4`

A clean way for `cmake` is to differentiate between `source`, `build` and
`install` directory. We will add the tar archive to `src`.

As root:

```bash
export BUILD_USER=USER_ID
export BUILD_GROUP=GROUP_ID
mkdir -p /srv/{build,source,src}/kdiff3-1.8.4
mkdir -p /srv/kdiff3-1.8.4/share
chown -R $BUILD_USER:$BUILD_GROUP /srv/{build,source,src}/kdiff3-1.8.4
```

On Debian 10 Buster an old version of `cmake` is used: 3.13.4, which do **not**
have the `--install` option. For the new version use: `cmake --install
/srv/build/kdiff3-1.8.4 --prefix /srv/kdiff3-1.8.4` (untested). The following
uses the old method.

As user `$BUILD_USER:$BUILD_GROUP`:

```bash
cd /srv/src
wget -nc https://download.kde.org/stable/kdiff3/kdiff3-1.8.4.tar.xz
cd /srv/source
tar xvJf ../src/kdiff3-1.8.4.tar.xz
cd /srv/build/kdiff3-1.8.4
cmake -DCMAKE_INSTALL_PREFIX:PATH=/srv/kdiff3-1.8.4 /srv/source/kdiff3-1.8.4
make
make install
```

#### Usage

After installation `kdiff3` can be started:

```bash
/srv/kdiff3-1.8.4/bin/kdiff3
```

#### Critique

This is critique is valid for Debian 10 Buster.

Positive

- The compilation is straight forward
- The code compiles fast
- The detection of dependencies seem to work
- The binary starts
- Unlike the original version on Debian 10 Buster,
  directory comparison works to some degree (but not all)

Negative

- A directory comparison with symbolic links throw an error in form of a GUI
  with a lot of messages like "Mix of Links and normal files" and the resulting
  `kdiff3` instance dropped the window managers (Mate) borders and made it hard
  to terminate via window manager the instance or move it. Termination via menu
  'file' and 'quit' worked. Subsequent starts were omitting the window manger
  decorations. Even a reboot of the system did not made the window manger
  reappear again. It just shows "QMainWindow::addToolBar: invalid 'area'
  argument" when started from command line. However the window manager
  decoration are visible when executing via 'ssh -X' from a remote system. The
  above error message stays, so probably this is not related.
- Binary comparison of files do **not** work. This limits the directory
  comparison mode. While `diff` is primary used to understand the difference
  between text files, it is mandatory to know if binary files are different (or
  not) when comparing directories.
- The overview info box regarding equal and different file count is not
  displayed
- The writing of the application changed from `kdiff3` to `KDiff3` without any
  need (This was also partly observed in the old version)

### Conclusion

In regard to the problems found in `KDiff3` 1.8.4 I would recommend to use the
old version `kdiff3` 0.9.98 on Debian 10 Buster. No guarantee. It seems to be
an example of software 'innovation' were the application now supports Qt5
instead of Qt4 and a "better" build system but a decrease of features and in
this case lacks fundamental features, like comparing binary files or handle
symbolic links.  Sometimes software is like wine, the older one is better.

### Update on kdiff3-1.8.5

The compilation is similar to kdiff3-1.8.4, some problems are solved, like the
vanishing border. However links let the application crash. So the former
conclusion is still valid. However, after using this version on Debian 11
Bullseye from package it could be that the build process introduced this
errors.

## Links

### Old Version

- [old home page]
- [Handbook 0.9.92]

### New Version

- [source]
- [download]
- [Handbook 1.08.02]
- [Handbook 1.08.70]
- [1.8.4]

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2022-06-20 | Shell->bash, update Debian 11, hierarchy             |
| 0.1.1   | 2020-12-31 | Compilation of KDiff3-1.8.4 on Debian 10 Buster      |
| 0.1.0   | 2020-07-07 | Initial release                                      |

[Project 0.9.98]: https://sourceforge.net/projects/kdiff3/
[old home page]: http://kdiff3.sourceforge.net/
[Handbook 0.9.92]: http://kdiff3.sourceforge.net/doc/index.html

[source]: https://invent.kde.org/sdk/kdiff3
[download]: https://download.kde.org/stable/kdiff3/
[Handbook 1.08.02]: https://docs.kde.org/stable5/en/extragear-utils/kdiff3/
[Handbook 1.08.70]: https://docs.kde.org/trunk5/en/extragear-utils/kdiff3/

[1.8.4]: https://download.kde.org/stable/kdiff3/kdiff3-1.8.4.tar.xz
