---
title: Kdiff3
author: Christian Külker
date: 2023-02-09
version: 0.1.3
type: doc
disclaimer: True
toc: True
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


On the command line, slim applications are usually preferred, such as the
`diff` command and its friends. However, sometimes (human eye) pattern matching
is not very good on the command line, and Gnome or other lightweight desktop
environments lack intuitive diff tools. KDE provides `kdiff3` and it has served
well under Gnome and Mate for Debian for a long time up to and including Debian
9.13 Stretch.

However, `kdiff3` is useless on Debian 10 Buster. I am not sure if this is just
buggy outside KDE or even inside KDE (it probably is).  However, `kdiff3` is
part of my toolchain, and when I work on Debian 10 Buster, I need to move the
task to Debian 9.13 Stretch, or in case of continuation on Debian 10 Buster, I
need to move files or whole directory structures to Debian 9.13 Stretch just
for comparison.

To avoid this inconvenience, I tried to find alternatives to `kdiff3` on Debian
10 Buster under Mate, but have not yet found a fully intuitive replacement. The
search included `meld`, `diffuse merge tool`, `vimdiff` and `kompare`. While
`meld` comes close, it is not very intuitive (to me). This document describes
some attempts to compile and use different versions of `kdiff3` on different
versions of Debian.

## Versions

| Debian      | kdiff3  | KDE     | Qt     | Window System   | Via     | Usage      |
| ----------- | ------- | ------- | ------ | --------------- | ------- | ---------- |
| 11 Bullseye | 1.8.5-1 | 5.111   |        |                 | package | PASS       |
| 10 Buster   | 1.8     | 5.37+   | 5.8    |                 | source  | PARTIAL OK |
| 10 Buster   | 1.7.90  | 5.54.9  | 5.11.3 | xcb             | package | FAIL       |
| 10 Buster   | 0.9.98  |         | 5      |                 | source  | PASS       |
| 9  Stretch  | 0.9.98  | 4.14.26 |        |                 | package | PASS       |

The above `window system` is information provided by the application's About
dialogue.

The old version 0.9.98 has its [old homepage] on Sourceforge. And it seems that
the feature freeze was around 2014-07-04. However, minor updates and
maintenance are done from time to time (updated 3 months ago - as of
2020-12-31).

The new version (Buster) seems to be under active development and offers a port
to Qt5, but lacks basic functionality (see conclusion later).

With Debian 11 Bullseye, `kdiff3-qt` aka `KDiff3` is no longer developed and
has been replaced by `kdiff3`.

## Kdiff3 for Debian 11 Bullseye

The current package for `kdiff3` is `kdiff3-1.8.5` and is currently 1.5 years
behind the latest source release 1.9.5 from 2022-02-23. As this is a
general-purpose tool, it may be advisable to read the
[manual](https://docs.kde.org/stable5/en/kdiff3/kdiff3/kdiff3.pdf), for example
to understand how to use `kdiff3` with git.

## Kdiff3 for Debian 10 Buster

### Building kdiff3 0.9.98 On Debian 10 Buster

#### Prerequisites

The prerequisite is that you have installed all the packages listed in the
section `Building KDdiff3 1.8.4 on Debian 10 Buster`.

### Compiling

This section presents two ways to compile the sources. The first will compile
and install the binary into `/usr/local`, the second will modify the `qmake`
project file and install the binary into `/srv/kdiff3-0.9.98`. For both, due to
limitations of the handcrafted `configure' script and `qmake', a custom prefix
cannot be set on the fly from the command line.

#### Preparation

For both locations, the following must be set up as root

```bash
export BUILD_USER=USER_ID
export BUILD_GROUP=GROUP_ID
mkdir -p /srv/{src,source,build,kdiff3-0.9.98}
chown $BUILD_USER:$BUILD_GROUP /srv/{build,source,src,kdiff3-0.9.98}
```

#### /usr/local

The easiest way is to use a local user `$BUILD_USER:$BUILD_GROUP` and build the
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

This will build `kdiff3` in `/srv/build/kdiff3-0.9.98/releaseQt/kdiff3` and it
can be used from there. If you need to install it, do it as root:

```bash
cd releaseQt
make install
```

#### /srv/kdiff3-0.9.98

The following procedure will compile and install to `/srv/kdiff3-0.9.98`.
Using a source and build directory is not strictly necessary, but helps to
understand what is being changed during the build process.

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
also runs directly from its build location:

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

This will download, configure and build `kdiff3-1.8.4` and install it in
`/srv/kdiff3-1.8.4`.

A clean way for `cmake` is to distinguish between the `source`, `build` and
`install` directories. We will add the tar archive to `src`.

As root:

```bash
export BUILD_USER=USER_ID
export BUILD_GROUP=GROUP_ID
mkdir -p /srv/{build,source,src}/kdiff3-1.8.4
mkdir -p /srv/kdiff3-1.8.4/share
chown -R $BUILD_USER:$BUILD_GROUP /srv/{build,source,src}/kdiff3-1.8.4
```

Debian 10 Buster uses an old version of `cmake`: 3.13.4, which **doesn't** have
the `--install` option. For the new version use `cmake --install
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
- Dependency detection seems to work
- The binary starts
- Unlike the original version on Debian 10 Buster,
  directory comparison works to some degree (but not all)

Negative

- A directory comparison with symbolic links threw an error in the form of a
  GUI with many messages like "Mix of links and normal files", and the
  resulting `kdiff3` instance dropped the window manager (mate) boundaries and
  made it difficult to quit or move the instance via the window manager.
  Quitting via the `file' menu and `quit' worked. Subsequent starts dropped the
  window manager decorations. Even rebooting the system did not make the window
  manager appear again. It just shows "QMainWindow::addToolBar: invalid 'area'
  argument" when started from the command line. However, the window manager
  decorations are visible when executed via 'ssh -X' from a remote system. The
  above error message remains, so this is probably unrelated.
- Binary comparison of files does **not** work. This limits the directory
  compare mode. While `diff` is primarily used to understand the difference
  between text files, it is essential to know whether binary files are
  different (or not) when comparing directories.
- The overview info box regarding equal and different file count is not
  displayed
- The writing of the application has been changed from `kdiff3` to `KDiff3` for
  no reason (this was also partly observed in the old version).

### Conclusion

In view of the problems found in `KDiff3` 1.8.4, I would recommend using the
old version `kdiff3` 0.9.98 on Debian 10 Buster. No guarantee. It seems to be
an example of software 'innovation' where the application now supports Qt5
instead of Qt4 and a 'better' build system, but a reduction in features and in
this case lacks basic features like comparing binary files or handling symbolic
links.  Sometimes software is like wine, the older the better.

### Update on kdiff3-1.8.5

The compilation is similar to kdiff3-1.8.4, some problems have been fixed, like
the vanishing border. However, links cause the application to crash. So the
previous conclusion is still valid. However, after using this version on Debian
11 Bullseye from package, it could be that the build process introduced these
bugs.

## Links

### Old Version

- [old homepage]
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
| 0.1.3   | 2023-02-09 | Improve writing                                      |
| 0.1.2   | 2022-06-20 | Shell->bash, update Debian 11, hierarchy             |
| 0.1.1   | 2020-12-31 | Compilation of KDiff3-1.8.4 on Debian 10 Buster      |
| 0.1.0   | 2020-07-07 | Initial release                                      |

[Project 0.9.98]: https://sourceforge.net/projects/kdiff3/
[old homepage]: http://kdiff3.sourceforge.net/
[Handbook 0.9.92]: http://kdiff3.sourceforge.net/doc/index.html

[source]: https://invent.kde.org/sdk/kdiff3
[download]: https://download.kde.org/stable/kdiff3/
[Handbook 1.08.02]: https://docs.kde.org/stable5/en/extragear-utils/kdiff3/
[Handbook 1.08.70]: https://docs.kde.org/trunk5/en/extragear-utils/kdiff3/

[1.8.4]: https://download.kde.org/stable/kdiff3/kdiff3-1.8.4.tar.xz
