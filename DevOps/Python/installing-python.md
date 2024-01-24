---
title: Installing Python
author: Christian Külker
date: 2023-08-02
version: 0.1.1
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

This document is about installing Python.

### History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2023-08-02 | Typos                                                |
| 0.1.0   | 2022-03-11 | Initial release                                      |


## Python v3.10.2 on Debian Stretch from Source

This document will probably also be valid for more recent versions of Debian.

### Dependencies

```
aptitude install make build-essential libssl-dev zlib1g-dev \
libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm \
libncurses5-dev libncursesw5-dev xz-utils tk-dev
```

### Getting the Source


Look at `https://www.python.org/ftp/python/` for the desired or latest version.

```
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

```
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





