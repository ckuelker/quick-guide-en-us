---
linkTitle: Linpack
title: High Performance Linpack
type: doc
author: Christian Külker
date: 2020-05-03
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
description: High Performance Linpack Benchmark

---

The High Performance Linpack (HPL) is the major benchmark to measure the
performance of super computers. As there are different hardware architectures,
different optimized version of the benchmark exist.

## Compile Linpack on CentOS 7

### Dependencies

```shell
    yum install wget
```

### Download

```shell
wget http://www.netlib.org/benchmark/hpl/hpl-2.1.tar.gz
```

### Compile

```shell
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

```
6=stdout
7=stderr
```
### Variables

Variables needs to match hardware. Example:

```
N      :   36992
NB     :     128
PMAP   : Row-major process mapping
P      :       1                    - compute nodes
Q      :      16                    - cores per node

```

## Example Linpack Configuration

Some Linpack binaries accept configuration in the form of a file. Having a name
like `cfg.dat` that needs to have a special format, like this for HPL.

```
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
```



