---
title: Mpich
linkTitle: Mpich
author: Christian Külker
date: 2023-01-26
version: 0.1.2
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- HPC
commands:
- make
- configure
tags:
- mpich
- mpich-4.1a1
packages:
- gfortran
- automake
- g++
- make
source:
- mpich-4.1a1.tar.gz
description: Installing and usage of Mpich

---

## Raspberri Pi MPICH 4.1a1

Building and installing `mpich-4.1a1` on a Raspberry Pi 4. The build is
done in `/opt/hpc/rpi/mpi/mpich/4.1a1/bld` and the install location
is `/opt/hpc/rpi/mpi/mpich/4.1a1` to control the version usage of `mpich`.

As root

```bash
aptitude install gfortran automake g++ make
mkdir -p /opt/hpc/src
chown -R $USER.$USER /opt/hpc
```

As user

```bash
export VER=4.1a1
cd /opt/hpc/src
wget https://www.mpich.org/static/downloads/$VER/mpich-$VER.tar.gz
export PFX=/opt/hpc/rpi/mpi/mpich/$VER
mkdir -p $PFX/bld
cd $PFX/bld
tar xvzf /opt/hpc/src/mpich-$VER.tar.gz --strip-components=1
```

On traditional `HPC` systems the device must match the network. Without
specification it might look like this:

```bash
./configure --prefix=$PFX
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

With using the legacy `ch3:sock` device it looks like this:

```bash
./configure  --prefix=$PFX --with-device=ch3:sock
...
*****************************************************
***
*** device configuration: ch3:sock
***
*****************************************************
Configuration completed.
```

If you are following these various `configure` experiments, make sure you run
`configure` from a clean archive before compiling.

```bash
make -j 4
make install
```

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2023-01-26 | Improve writing                                      |
| 0.1.1   | 2022-06-20 | Change dir structure                                 |
| 0.1.0   | 2022-06-18 | Initial release                                      |
