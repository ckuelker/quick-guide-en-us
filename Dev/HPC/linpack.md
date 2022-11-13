---
linkTitle: Linpack
title: High Performance Linpack
type: doc
author: Christian Külker
date: 2022-06-18
version: 0.1.1
keywords:
categories:
    - HPC
    - Benchmark
tags:
    - Linpack
    - High Performance Linpack
    - CentOS 7
    - MKL
    - Intel
    - Intel Optimized Linpack For Linux
    - Intel Optimized Linpack For Clusters
    - AMD
    - Rpi4
    - HPL
    - hpl-2.1
    - hpl-2.3
    - mpich
    - mpich-4.1a1
    - atlas
    - atlas-3.10.3
commands:
- aptitude
- configure
- cpufreq-set
- make
- numactl
- tar
- wget
description: High Performance Linpack Benchmark

---

The High Performance Linpack (HPL) is the major benchmark to measure the
performance of super computers. As there are different hardware architectures,
different optimized version of the benchmark exist.

Running HPL is at least a 6 dimensional problem, where the 6 major dimensions
are:

1. Architecture
2. Message Passing Interface (MPI) Library
3. Linear Algebra (LA) Library
4. Compiler/ Linker
5. HPL
6. Benchmark Problem Size

Unfortunately this 6 dimensions are only partly dependent to each other, some
are not. For example depends the problem size on the hardware size (nodes,
memory, cores) but usually not on the HPL version. While the `xhpl` binary
depends on the HPL version, MPI, LA and the compiler/linker, but not on the
hardware memory size (unless optimized). The problem size configuration
(HPL.dat) is not dependent on the MPI or vice versa. Therefore a strictly
hierarchical directory structure to run HPL seems not possible. The following
diagram shows roughly the dependencies in round brackets and is the proposed
directory structure of described linpack runs. This is as for the Raspberry Pi
(rpi) (for educational purpose).

~~~
opt
  hpc
     src
       hpl-2.3.tar.gz
     bin
       run-create
     rpi [=arch] <dim0>
       mpi [=sw:mpi] <dim1>
         openipmi (arch) -> deb
         mpich (arch) <- tar
       la [=sw:la] <dim2>
         openblas-pthread (arch) -> deb
         atlas (arch) <- tar
       compiler/linker <dim3>
          c
          c++
          f77
       cfg <dim4>
         1n [=infrastructure[=inf]:node]
           8gb [=hw:mem]
             4c [=hw:core]
               1 (arch,inf,mem,core)
                 HPL.dat (arch,inf,mem,core)
       openmpi
         openblas-pthread
           hpl (arch,mpi,la(blas|openblas|atlas) [=sw:bm] <dim5>
             2.3 [=sw:bm:ver])
               bld
                 Make.rpi (arch,mpi,la,compiler)
                 bin/rpi/xhpl (arch,mpi,la,compiler,sw:bm:ver)
               run
                 1  (arch,mpi,la,compiler,inf,mem,core)
                   run
                   xhpl     (arch.mpi,la,compiler)
                   HPL.dat  (arch,inf,mem,core)
                   Make.rpi (arch,mpi,la,compiler)
~~~



## HPL 2.3 On Raspberry Pi

### Preparation

As root

```bash
mkdir -p /opt/hpc/hpl/src
mkdir -p /opt/hpc/hpl/2.3/openmpi/openblas-pthread/r/
chown -R $USER.$USER /opt/hpc/
```

As user `$USER`:

```bash
cd /opt/hpc/hpl/src
wget http://www.netlib.org/benchmark/hpl/hpl-2.3.tar.gz
```

### Openmpi & Openblas-pthread

```bash
aptitude install openmpi-bin libopenblas-dev openmpi-common libopenmpi-dev \
```

This will install mostly to `/usr/lib/aarch64-linux-gnu/`.

### HPL 2.3 with Openmpi & Openblas-pthread

```bash
aptitude install automake
cd /opt/hpc/hpl/2.3/openmpi/openblas-pthread/
tar xvzf /opt/hpc/src/hpl-2.3.tar.gz
cd hpl-2.3/setup
sh make_generic
cp Make.UNKNOWN ../Make.rpi
```

Change the following values in
[Make.rpi](opt/hpc/hpl/2.3/openmpi/openblas-pthread/hpl-2.3/Make.rpi):

~~~
ARCH         = rpi

TOPdir       = /opt/hpc/hpl-2.3-openmpi-openblas

# /usr/lib/aarch64-linux-gnu/openmpi
MPdir        = /usr/lib/aarch64-linux-gnu/openmpi
# Search for include in openmpi
MPinc        = -I $(MPdir)/include
# Serach for libmpi.a or libmpi.so
# /usr/lib/aarch64-linux-gnu/openmpi/lib/libmpi.so ../../libmpi.so.40
MPlib        = $(MPdir)/lib/libmpi.so

LAdir        =
# 3. /usr/lib/aarch64-linux-gnu/libblas.a
LAinc        = /usr/include/aarch64-linux-gnu/
LAlib        = -lblas
# 2. /usr/lib/aarch64-linux-gnu/atlas/libblas.a
LAinc        = /usr/include/aarch64-linux-gnu/atlas
LAlib        = -lblas
# 1. /usr/lib/aarch64-linux-gnu/openblas-pthread/libblas.a
LAinc        = /usr/include/aarch64-linux-gnu/openblas-pthread
LAlib        = -lblas
~~~

```bash
make arch=rpi
...
make[1]: Leaving directory '/opt/hpc/hpl/2.3/openmpi/openblas-pthread/hpl-2.3'
```

Next it is possible to run HPL on a single node. Prepare some variables and a
`hostfile`. Execute this manually or create a start script. If the binary is
not too big, it also might be advisable to copy the `xhpl` binary and
`Make.rpi` file to the run directory, to recreate  or recheck the run on
different hardware. An alternative is to make a note about the binary path an
never recompile the archive under that directory ...

```bash
#!/usr/bin/zsh
export ARCH=rpi
export DIR=/opt/hpc/hpl/2.3/openmpi/openblas-pthread/
export BDIR=$DIR/hpl-2.3/bin/$ARCH
export BIN=$BDIR/xhpl
export RUN=0001
export RDIR=$DIR/r/$RUN
export MPI=/usr/bin/mpiexec
export HF=rpi4-8gb-1n-localhost.nodes
mkdir -p $RDIR
if [ -f $HF ];then rm $HF;fi
touch $HF
for i in {1..4}; do echo localhost >> $HF;done
cp $BIN .
cp $DIR/hpl-2.3/Make.rpi .
```




Run the binary.

```bash
cd $RDIR
$MPI --hostfile $HF $BIN
================================================================================
HPLinpack 2.3  --  High-Performance Linpack benchmark  --   December 2, 2018
Written by A. Petitet and R. Clint Whaley,  Innovative Computing Laboratory, UTK
Modified by Piotr Luszczek, Innovative Computing Laboratory, UTK
Modified by Julien Langou, University of Colorado Denver
================================================================================

An explanation of the input/output parameters follows:
T/V    : Wall time / encoded variant.
N      : The order of the coefficient matrix A.
NB     : The partitioning blocking factor.
P      : The number of process rows.
Q      : The number of process columns.
Time   : Time in seconds to solve the linear system.
Gflops : Rate of execution for solving the linear system.

The following parameter values will be used:

N      :    5120
NB     :     128
PMAP   : Row-major process mapping
P      :       2
Q      :       2
PFACT  :   Right
NBMIN  :       4
NDIV   :       2
RFACT  :   Crout
BCAST  :  1ringM
DEPTH  :       1
SWAP   : Mix (threshold = 64)
L1     : transposed form
U      : transposed form
EQUIL  : yes
ALIGN  : 8 double precision words

--------------------------------------------------------------------------------

- The matrix A is randomly generated for each test.
- The following scaled residual check will be computed:
      ||Ax-b||_oo / ( eps * ( || x ||_oo * || A ||_oo + || b ||_oo ) * N )
- The relative machine precision (eps) is taken to be               1.110223e-16
- Computational tests pass if scaled residuals are less than                16.0

================================================================================
T/V                N    NB     P     Q               Time                 Gflops
--------------------------------------------------------------------------------
WR11C2R4        5120   128     2     2              26.71             3.3515e+00
HPL_pdgesv() start time Sun Jun 19 23:53:01 2022

HPL_pdgesv() end time   Sun Jun 19 23:53:27 2022

--------------------------------------------------------------------------------
||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   4.41990053e-03 ...... PASSED
================================================================================

Finished      1 tests with the following results:
              1 tests completed and passed residual checks,
              0 tests completed and failed residual checks,
              0 tests skipped because of illegal input values.
--------------------------------------------------------------------------------

End of Tests.
================================================================================


```





TODO:

Change approach

A) Use Fast approach

Follow:  https://www.hydromag.eu/~aa3025/rpi/

B) Use Slow approch > 15h

Follow https://mikejmcfarlane.github.io/blog/2020/09/17/High-Performance-Linpack-for-raspberry-pi-supercomputer
https://computenodes.net/2018/06/28/building-hpl-an-atlas-for-the-raspberry-pi/

- Altlas localtion was wrong (was /tmp/atlas-build, should /opt/hpc/atlas-build)
- Manuall ln to libs was probably wrong
- Recompile all
  - /opt/hpc/mpich-4...
  - /opt/hpc/atlas-build
  - /opt/hpc/hpl-2.3-mpich4-atlas

C) Compare with https://forums.raspberrypi.com/viewtopic.php?t=276089





######################






## HPL 2.3 Debian 11 on Raspberry Pi 4 8GB

### openmpi openblas






### mpich-4.1a1 atlas-3.10.3


This usage of HPL on a raspberry Pi 4 8GB is for educational purpose. It is
assumed that [mpich-4.1a1](mpich.md) and [atlas-3.10.3](atlas.md) has been
installed successfully.

```bash
mkdir -p /tmp/hpc # should be already done
cd /tmp/hpc
wget http://www.netlib.org/benchmark/hpl/hpl-2.3.tar.gz
tar xvzf hpl-2.3.tar.gz
cd hpl2.3/setup
sh make_generic
cp Make.UNKNOWN ../Make.rpi
cd ..
```

On my Debian 11 Bullseye installation the following links had been made as
a workaround:

```bash
cd /usr/lib/aarch64-linux-gnu
ln -s libevent_pthreads-2.1.so.7.0.1 libevent_pthreads.so
ln -s libevent_core-2.1.so.7.0.1 libevent_core.so
ln -s libhwloc.so.15.4.1 libhwloc.so
ln -s libopen-pal.so.40.30.0 libopen-pal.so
ln -s libopen-rte.so.40.30.0 libopen-rte.so
ln -s libmpi_mpifh.so.40.30.0 libmpi_mpifh.so
ln -s libmpi_usempi_ignore_tkr.so.40.30.0 libmpi_usempi_ignore_tkr.so
ln -s libmpi_usempif08.so.40.30.0 libmpi_usempif08.so
```

Some values of [Make.rpi](Make.rpi) have to change.

```make
ARCH         = rpi
TOPdir       = /tmp/hpc/hpl-2.3
MPdir        = /tmp/hpc
MPinc        = -I /tmp/hpc/include
MPlib        = /tmp/hpc/lib/libmpich.so
LAdir        = /tmp/hpc/atlas-build
LAlib        = $(LAdir)/lib/libf77blas.a $(LAdir)/lib/libatalas.a
```
Run `make arch=rpi`. If in some cases the configuration is not OK,
make sure you run from a clean archive.

```bash
make arch=rpi
```

Create a [HPL.dat](HPL.dat) file.

~~~
HPLinpack benchmark input file
Innovative Computing Laboratory, University of Tennessee
HPL.out      output file name (if any)
6            device out (6=stdout,7=stderr,file)
1            # of problems sizes (N)
5120         Ns
1            # of NBs
128          NBs
0            PMAP process mapping (0=Row-,1=Column-major)
1            # of process grids (P x Q)
2            Ps
2            Qs
16.0         threshold
1            # of panel fact
2            PFACTs (0=left, 1=Crout, 2=Right)
1            # of recursive stopping criterium
4            NBMINs (>= 1)
1            # of panels in recursion
2            NDIVs
1            # of recursive panel fact.
1            RFACTs (0=left, 1=Crout, 2=Right)
1            # of broadcast
1            BCASTs (0=1rg,1=1rM,2=2rg,3=2rM,4=Lng,5=LnM)
1            # of lookahead depth
1            DEPTHs (>=0)
2            SWAP (0=bin-exch,1=long,2=mix)
64           swapping threshold
0            L1 in (0=transposed,1=no-transposed) form
0            U  in (0=transposed,1=no-transposed) form
1            Equilibration (0=no,1=yes)
8            memory alignment in double (> 0)
~~~

```bash
cd
ln -s libmpi.so.40.30.0 libmpi.so.0
```


```
mkdir /opt/hpl/hpl-run/rpi8gb
cp HPL.dat /opt/hpl/hpl-run/rpi8gb
cd /opt/hpl/hpl-run/rpi8gb

mpiexec --host localhost /tmp/hpc/hpl-2.3/bin/rpi/xhpl
--------------------------------------------------------------------------
Primary job  terminated normally, but 1 process returned
a non-zero exit code. Per user-direction, the job has been aborted.
--------------------------------------------------------------------------
--------------------------------------------------------------------------
mpiexec noticed that process rank 0 with PID 0 on node c3 exited on signal 11 (Segmentation fault).
--------------------------------------------------------------------------



mpirun -np 4 /tmp/hpc/hpl-2.3/bin/rpi/xhpl
--------------------------------------------------------------------------
Primary job  terminated normally, but 1 process returned
a non-zero exit code. Per user-direction, the job has been aborted.
--------------------------------------------------------------------------
--------------------------------------------------------------------------
mpirun noticed that process rank 2 with PID 0 on node c3 exited on signal 11 (Segmentation fault).
--------------------------------------------------------------------------

```








For this approach

mpich-4.1a1
atlas-3.10.3


HPL 2.3 from December 2, 2018








#####################


## MPICH Openmpi

```bash
aptitude install openmpi-bin libopenblas-dev
```





### Dependencies:

```bash
aptitude install libatlas-base-dev libmpich-dev gfortran
```

Will install




~~~
gfortran gfortran-10{a} hwloc-nox{a} libatlas-base-dev libatlas3-base{a}
libgfortran-10-dev{a} libhwloc-plugins{a} libhwloc15{a} libmpich-dev
libmpich12{a} libslurm36{a} libxnvctrl0{a} mpich{a}
~~~

HPL 2.3 from December 2, 2018

```bash
wget https://www.netlib.org/benchmark/hpl/hpl-2.3.tar.gz
tar xvzf hpl-2.3.tar.gz
```

Create a file Make.ARCH in the  top-level directory

```bash
cd hpl-2.1/setup
sh make_generic
cp Make.UNKNOWN ../Make.rpi
cd ..
./configure --prexif=/tmp/hpl
```



## Compile Linpack 2.1 on CentOS 7

### Dependencies

```bash
yum install wget
```

### Download

```bash
wget http://www.netlib.org/benchmark/hpl/hpl-2.1.tar.gz
```

### Compile

```bash
tar xf hpl-2.1.tar.gz
cd hpl-2.1/setup
sh make_generic
cp Make.UNKNOWN ../Make.Linux
cd ..
```

## Intel Optimized Linpack For Linux / Clusters

This benchmark need the Intel(R) Math Kernel Library (MKL) 10.3 update 4 for
Linux.

* [MKL Download](http://software.intel.com/en-us/articles/intel-math-kernel-library-linpack-download/)
* [Intel Linkpack Download](http://registrationcenter-download.intel.com/akdlm/irc_nas/2169/l_lpk_p_10.3.4.007.tgz)
* [MKL](http://www.intel.com/software/products/mkl)

### Device Output Codes

~~~
6=stdout
7=stderr
~~~

### Variables

Variables needs to match hardware. Example:

~~~
N      :   36992
NB     :     128
PMAP   : Row-major process mapping
P      :       1                    - compute nodes
Q      :      16                    - cores per node
~~~

## Example Linpack Configuration

Some Linpack binaries accept configuration in the form of a file. Having a name
like `cfg.dat` that needs to have a special format, like this for HPL.

~~~
HPL.out      output file name (if any)
6            device out (6=stdout,7=stderr,file)
3            # of problems sizes (N)
82688 82880 82720 Ns
3            # of NBs
128 160 176 NBs
0            PMAP process mapping (0=Row-,1=Column-major)
1            # of process grids (P x Q)
4        Ps
4        Qs
16.0         threshold
1            # of panel fact
2            PFACTs (0=left, 1=Crout, 2=Right)
1            # of recursive stopping criterium
4            NBMINs (>= 1)
1            # of panels in recursion
2            NDIVs
1            # of recursive panel fact.
1            RFACTs (0=left, 1=Cr
~~~

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2022-06-18 | History, shell->bash, Debian 11, hpl-2.3,            |
|         |            | mpich-4.1a1 and atlas-3.10.3 on AMD and Rpi4         |
| 0.1.0   | 2020-05-03 | Initial release                                      |


