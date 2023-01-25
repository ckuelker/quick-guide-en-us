---
title: Atlas
linkTitle: Atlas
author: Christian Külker
date: 2023-01-25
version: 0.1.1
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- HPC
commands:
- configure
- cpufreq-set
- make
- numctl
- screen
- tmux
- wget
tags:
- Atlas
- BLAS
- mpich
packages:
- cpufrequtils
source:
- atlas3.10.3.tar.bz2
description: Atlas

---

## Introduction

Compiling HPC components on a Raspberry Pi do not make sense, as the Raspberry
Pi is not powerful. However the way and consideration when building HPC
software components, like Atlas, is very similar as on Intel or AMD based HPC
systems. For educational purpose this document describes building Altlas 3 on
the Raspberry Pi 4. Be patient, compiling Atlas takes time and depends on
single core performance.

## rpi/la/atlas/3.10.3

This builds and installs Atlas 3.10.3 for the Raspberry Pi 4. However, except
setting a non throttling mode, this is similar to other architectures.

Atlas 3.10.3 from 2016 is still the latest (2022-06-18) version.

For this compilation [mpich](mpich.md) was installed first. Dependencies to
[mpich](mpich.md) are are not listed here.

For Atlas to be useful in HPC, consider a homogeneous cluster. Atlas should be
compiled from source for every hardware architecture as Atlas make timing
calculation during build time. The length of build depends highly on single
core performance. On a Raspberry Pi 4 8GB it can take 15hours and 44minutes,
while on a modern AMD 6hours and 30minutes.

Preparations as root

```bash
mkdir -p /opt/hpc/src
chown -R $USER.$USER /opt/hpc
apitude install cpufrequtils
```

Make sure you set the performance and disable CPU throttling. Assuming a
certain hardware you can get the numbers of cores via a command (or you have to
figure out via `/proc/cpuinfo`)

1. Either try `cpufreq` to disable throttling

```bash
numactl --hardware|grep cpus|sed -e 's%node 0 cpus:%%'
 0 1 2 3 4 5 6 7 8 9 10 11
for c in `numactl --hardware|grep cpus|sed -e 's%node 0 cpus:%%'`;do\
/usr/bin/cpufreq-set -g performance $c;done
```

2. Or set `performance` manually:

```bash
numactl --hardware|grep cpus|sed -e 's%node 0 cpus:%%'
 0 1 2 3 4 5 6 7 8 9 10 11

for c in `numactl --hardware|grep cpus|sed -e 's%node 0 cpus:%%'`;do\
echo performance|sudo /sys/devices/system/cpu/cpu$c/cpufreq/scaling_governor;\
done

for c in `numactl --hardware|grep cpus|sed -e 's%node 0 cpus:%%'`;do \
echo -n "CPU $c ";cat /sys/devices/system/cpu/cpu$c/cpufreq/scaling_governor;\
done
```

3. Or set via kernel parameter (as described in ATLAS/doc/atlas_install.pdf
   page 5 (not tested).

4. Or use `BLAS` (as performance can not be guaranteed anyways, it throttling
   can not be disabled.)

5. Or if you insist on ATLAS, disable timing with `--cripple-atlas-performance`

If throttling is __not__ disabled, and you are not using
`--cripple-atlas-performance`, you might see this error (copied from a non
Raspberry Pi):

~~~
ERROR: enum fam=0, chip=32765, model=113, mach=-1785083552
make[3]: *** [Makefile:106: atlas_run] Error 100
make[2]: *** [Makefile:449: IRunArchInfo_x86] Error 2
CPU Throttling apparently enabled!
~~~

Either check the above list, the Atlas PDF `doc/atlas_install.pdf` included in
the archive, the more up to date [online
documentation](http://math-atlas.sourceforge.net/atlas_install/atlas_install.html),
use `BLAS` or compile with `--cripple-atlas-performance`.

When building Atlas, do not use the `-j` option, as this will mess up Atlas
timings. The make run will take some time. Make sure the system stays up that
long and is not used by other processes. It might make sense to execute it in
`screen` or `tmux`.

As user

```bash
export VER=3.10.3
export PFX=/opt/hpc/rpi/la/atlas/$VER
mkdir -p $PFX/{bld,arc}
cd /opt/hpc/src
wget https://sourceforge.net/projects/math-atlas/files/Stable/$VER/atlas$VER.tar.bz2
cd $PFX/arc
tar xvjf /opt/hpc/src/atlas$VER.tar.bz2 --strip-components=1
cd $PFX/bld
../arc/configure --prefix=$PFX
time make
...
make[2]: Leaving directory '/opt/hpc/rpi/la/atlas/3.10.3/bld/bin'
   DONE  STAGE 5-1-0 at 05:57

ATLAS install complete.  Examine
ATLAS/bin/<arch>/INSTALL_LOG/SUMMARY.LOG for details.
make[1]: Leaving directory '/opt/hpc/rpi/la/atlas/3.10.3/bld'
make clean
make[1]: Entering directory '/opt/hpc/rpi/la/atlas/3.10.3/bld'
rm -f *.o x* config?.out *core*
make[1]: Leaving directory '/opt/hpc/rpi/la/atlas/3.10.3/bld'
make check # perform sanity tests (optional)
make ptcheck # checks of threaded code (optional)
make time # provide performance summary (optional)
make install
```

After a complete build the following might be installed:

```bash
/opt/hpc/rpi/la/atlas/3.10.3/include/cblas.h
/opt/hpc/rpi/la/atlas/3.10.3/include/clapack.h
/opt/hpc/rpi/la/atlas/3.10.3/include/atlas/* # 161 files.
/opt/hpc/rpi/la/atlas/3.10.3/lib/libatlas.a
/opt/hpc/rpi/la/atlas/3.10.3/lib/libcblas.a
/opt/hpc/rpi/la/atlas/3.10.3/lib/liblapack.a
/opt/hpc/rpi/la/atlas/3.10.3/lib/libf77blas.a
/opt/hpc/rpi/la/atlas/3.10.3/lib/libptcblas.a
/opt/hpc/rpi/la/atlas/3.10.3/lib/libptf77blas.a
/opt/hpc/rpi/la/atlas/3.10.3/lib/libsatlas.dylib # sometimes not build
/opt/hpc/rpi/la/atlas/3.10.3/lib/libtatlas.dylib # sometimes not build
/opt/hpc/rpi/la/atlas/3.10.3/lib/libsatlas.dll # sometimes not build
/opt/hpc/rpi/la/atlas/3.10.3/lib/libtatlas.dll # sometimes not build
/opt/hpc/rpi/la/atlas/3.10.3/lib/libsatlas.so # sometimes not build
/opt/hpc/rpi/la/atlas/3.10.3/lib/libtatlas.so # sometimes not build
```

### Make time

As root:

```bash
cat /sys/devices/system/cpu/cpu0/cpufreq/cpuinfo_max_freq
1500000
cat /sys/devices/system/cpu/cpu0/cpufreq/cpuinfo_cur_freq
1500000
```

As user

```bash
make time
make -f Make.top time
make[1]: Entering directory '/opt/hpc/rpi/la/atlas/3.10.3/bld'
./xatlbench -dc /opt/hpc/rpi/la/atlas/3.10.3/bld/bin/INSTALL_LOG \
-dp /opt/hpc/rpi/la/atlas/3.10.3/bld/ARCHS/UNKNOWN64
Enter Clock rate in Mhz [0]: 1500

The times labeled Reference are for ATLAS as installed by the authors.
NAMING ABBREVIATIONS:
   kSelMM : selected matmul kernel (may be hand-tuned)
   kGenMM : generated matmul kernel
   kMM_NT : worst no-copy kernel
   kMM_TN : best no-copy kernel
   BIG_MM : large GEMM timing (usually N=1600); estimate of asymptotic peak
   kMV_N  : NoTranspose matvec kernel
   kMV_T  : Transpose matvec kernel
   kGER   : GER (rank-1 update) kernel
Kernel routines are not called by the user directly, and their
performance is often somewhat different than the total
algorithm (eg, dGER perf may differ from dkGER)


Clock rate=1500Mhz
               single precision        double precision
            *********************    ********************
               real      complex       real      complex
Benchmark   %   Clock   %   Clock   %   Clock   %   Clock
=========   =========   =========   =========   =========
  kSelMM       460.6      405.2      291.5      276.6
  kGenMM       154.6      152.4      147.4      135.9
  kMM_NT       142.4      136.8      126.0      121.8
  kMM_TN       150.4      145.2      133.8      133.1
  BIG_MM       430.2      425.7      282.5      286.9
   kMV_N        84.6      126.6       66.2       92.9
   kMV_T        99.3      126.5       61.3      109.6
    kGER        44.9       89.9       22.0       48.6
make[1]: Leaving directory '/opt/hpc/rpi/la/atlas/3.10.3/bld'
```

## Installation from Package Dependencies

Installing `atlas` on Debian 11 (Bullseye) will also pull in `mpich`.

```bash
aptitude install libatlas-base-dev libmpich-dev gfortran
```

This will install:

~~~
gfortran gfortran-10{a} hwloc-nox{a} libatlas-base-dev libatlas3-base{a}
libgfortran-10-dev{a} libhwloc-plugins{a} libhwloc15{a} libmpich-dev
libmpich12{a} libslurm36{a} libxnvctrl0{a} mpich{a}
~~~


## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2023-01-25 | Note for package installation of atlas               |
| 0.1.0   | 2022-06-19 | Initial release                                      |

