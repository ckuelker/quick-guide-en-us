---
title: Mpich
linkTitle: Mpich
author: Christian Külker
date: 2022-06-18
version: 0.1.0
locale: en_US
lang: en
type: doc
disclaimer: True
TOC: True
categories:
- HPC
commands:
- make
tags:
- mpich
- mpich-4.1a1
description: Installing and usage of Mpich

---

## mpich-4.1a1

```bash
aptitude install gfortran automake g++ make
mkdir -p /tmp/hpc
cd /tmp/hpc
wget https://www.mpich.org/static/downloads/4.1a1/mpich-4.1a1.tar.gz
tar xvzf mpich-4.1a1.tar.gz
cd mpich-4.1a1
```

On traditional `HPC` systems the device needs to match the network.  Without
specification it might look like:

```bash
./configure --prefix=/tmp/hpc
...
*****************************************************
***
*** device      : ch4:ofi (embedded libfabric)
*** shm feature : auto
*** gpu support : disabled
***
  MPICH is configured with device ch4:ofi, which should work
  for TCP networks and any high-bandwidth interconnect
  supported by libfabric. MPICH can also be configured with
  "--with-device=ch4:ucx", which should work for TCP networks
  and any high-bandwidth interconnect supported by the UCX
  library. In addition, the legacy device ch3 (--with-device=ch3)
  is also available.
*****************************************************
Configuration completed.
```

With using the legacy ch3:sock device it looks like:

```bash
./configure  --prefix=/tmp/hpc --with-device=ch3:sock
...
*****************************************************
***
*** device configuration: ch3:sock
***
*****************************************************
Configuration completed.
```

In case you follow those different `configure` experiments
make sure before compiling that `configure` is executed
from a clean archive.

```bash
make -j 4
make install
```

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2022-06-18 | Initial release                                      |
