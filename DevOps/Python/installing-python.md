---
title: Installing Python
author: Christian Külker
date: 2024-01-25
version: 0.1.2
type: doc
disclaimer: True
toc: True
categories:
- Python
commands:
- python
tags:
- Installing-Python
description: Installing Python on Debian

---

## Abstract

This document is about installing Python from source.

### History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2024-01-25 | Python 3.12.1 on Debian 12 Bookworm                  |
| 0.1.1   | 2023-08-02 | Typos                                                |
| 0.1.0   | 2022-03-11 | Initial release (Python 3.10.2, Debian Stretch)      |

## Python v3.12.1 on Debian 12 Bookworm from Source

### Dependencies

```bash
aptitude install make build-essential libssl-dev zlib1g-dev \
libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm \
libncurses5-dev libncursesw5-dev xz-utils tk-dev
```

### Deciding the Version

Look at [https://www.python.org/ftp/python/](https://www.python.org/ftp/python/) to chose the version of
Python you would like to compile.

### Getting the Source

```bash
export VER=3.12.1
export DIR=Python-$VER
export TAR=$DIR.tar.xz
export URL=https://www.python.org/ftp/python/$VER/$TAR
mkdir -p src
mkdir -p build
cd src
wget $URL
cd ../build
tar xvJf ../src/$TAR
cd $DIR
```

### Configure

The `--enable-optimizations` flag will enable some optimizations within Python
to make it run about 10 percent faster. Doing this may add twenty or thirty
minutes to the compilation time. The `--with-ensurepip=install` flag will
install pip bundled with this installation.

This might take 10-20 seconds:

```
cd /srv/build/$DIR
 ./configure --enable-optimizations --with-ensurepip=install
```

### Build

This might take 2-3 hours (2:37h for a 2014 PC)

```
make -j 4
```

This takes about 15 minutes on a "AMD FX(tm)-4300" Quad-Core Processor and 17
minutes on an AMD Athlon(tm) X2 Dual Core Processor BE-2400.

### Build Tests

Test the build with `make test` that executes 427 tests. This might need an
internet connection. The tests are more demanding to the system as the
compilation.

```bash
make test
```

The output with `time` might look like on an AMD dual core BE-2400:

~~~
== Tests result: SUCCESS ==

20 tests skipped:
    test.test_asyncio.test_windows_events
    test.test_asyncio.test_windows_utils test.test_gdb.test_backtrace
    test.test_gdb.test_cfunction test.test_gdb.test_cfunction_full
    test.test_gdb.test_misc test.test_gdb.test_pretty_print
    test_dbm_gnu test_dbm_ndbm test_devpoll test_ioctl test_kqueue
    test_launcher test_lzma test_msilib test_startfile
    test_winconsoleio test_winreg test_wmi test_zoneinfo

6 tests skipped (resource denied):
    test_ossaudiodev test_tix test_tkinter test_ttk test_winsound
    test_zipfile64

463 tests OK.

Total duration: 15 min 11 sec
Total tests: run=40,432 skipped=1,270
Total test files: run=483/489 skipped=20 resource_denied=6
Result: SUCCESS
make test  1452.83s user 171.06s system 177% cpu 15:12.57 total
~~~

See 'Python v3.10.2 on Debian Stretch from Source' for different
test invocations and alternative installation.

## Python v3.10.2 on Debian Stretch from Source

This guide will probably also be valid for more recent versions of Debian.

### Dependencies

```bash
aptitude install make build-essential libssl-dev zlib1g-dev \
libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm \
libncurses5-dev libncursesw5-dev xz-utils tk-dev
```

### Getting the Source

Look at [https://www.python.org/ftp/python/](https://www.python.org/ftp/python/) for the desired or latest version.

```bash
export VER=3.10.2
export DIR=Python-$VER
export TAR=$DIR.tar.xz
export URL=https://www.python.org/ftp/python/$VER/$TAR
mkdir src build
cd src
wget $URL
cd ../build
tar xvJf ../src/$TAR
cd $DIR
```

### Configure

The `--enable-optimizations` flag will enable some optimizations within Python
to make it run about 10 percent faster. Doing this may add twenty or thirty
minutes to the compilation time. The `--with-ensurepip=install` flag will
install pip bundled with this installation.


This might take 10-20 seconds:

```
cd /srv/build/$DIR
 ./configure --enable-optimizations --with-ensurepip=install
```

This might take 2-3 hours (2:37h for a 2014 PC)

```
make -j 4
```

Test the build with 427 tests. This might need an internet connection.

```bash
make test
```

The output might look like:

~~~
== Tests result: SUCCESS ==

407 tests OK.

20 tests skipped:
    test_dbm_gnu test_dbm_ndbm test_devpoll test_gdb test_ioctl
    test_kqueue test_lzma test_msilib test_ossaudiodev test_smtpnet
    test_ssl test_startfile test_tix test_tk test_ttk_guionly
    test_winconsoleio test_winreg test_winsound test_zipfile64
    test_zoneinfo

Total duration: 4 min 23 sec
Tests result: SUCCESS
~~~

The build binary is located at `build/$DIR/python` and a test can be executed
with the `-m` option. This is basically the same as `make test`.

```
./python -m test
```

~~~
== Tests result: SUCCESS ==

403 tests OK.

24 tests skipped:
    test_curses test_dbm_gnu test_dbm_ndbm test_devpoll test_gdb
    test_kqueue test_lzma test_msilib test_ossaudiodev test_smtpnet
    test_socketserver test_ssl test_startfile test_tix test_tk
    test_ttk_guionly test_urllib2net test_urllibnet test_winconsoleio
    test_winreg test_winsound test_xmlrpc_net test_zipfile64
    test_zoneinfo

Total duration: 24 min 17 sec
Tests result: SUCCESS
~~~

Interestingly `./python -m test` runs less tests than `make test`.

If you just want to use Python in a limited way, you are done. Maybe
write an environment to source and put the new python version in
your path.

If you need all functionality you might use the `altinstall` target here to
avoid overwriting the system Python. Since you are installing into
`/usr/local`, you will need to run as root (alternatively use `--prefix` with
`./configure`):

```
make altinstall
```

This will result in a bunch of warnings

~~~
WARNING: Value for scheme.headers does not match. Please report this to <https://github.com/pypa/pip/issues/10151>
distutils: /usr/local/include/python3.10/UNKNOWN
sysconfig: /srv/build/Python-3.10.2/Include/UNKNOWN
WARNING: Additional context:
user = False
home = None
root = '/'
prefix = None
Looking in links: /tmp/tmpwdn4wp92
Processing /tmp/tmpwdn4wp92/setuptools-58.1.0-py3-none-any.whl
Processing /tmp/tmpwdn4wp92/pip-21.2.4-py3-none-any.whl
Installing collected packages: setuptools, pip
  WARNING: Value for scheme.headers does not match. Please report this to <https://github.com/pypa/pip/issues/10151>
  distutils: /usr/local/include/python3.10/setuptools
  sysconfig: /srv/build/Python-3.10.2/Include/setuptools
  WARNING: Value for scheme.headers does not match. Please report this to <https://github.com/pypa/pip/issues/10151>
  distutils: /usr/local/include/python3.10/pip
  sysconfig: /srv/build/Python-3.10.2/Include/pip
Successfully installed pip-21.2.4 setuptools-58.1.0
WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv
~~~

