---
title: VASP Benchmark
linkTitle: VASP
author: Christian Külker
date: 2020-05-03
version: 0.1.0
type: doc
disclaimer: True
toc: True
categories:
- HPC
- Benchmark
tags:
- VASP
- Vienna Ab-initio Simulation Package
- MPI
- FORTRAN 90
- MD
- Bright Cluster Manager
- Intel Cluster
- Infiniband
- GbE
description: Vienna Ab-initio Simulation Package

---

**VASP** stands for **Vienna Ab-initio Simulation Package** and is a code
written in FORTRAN 90 with MPI support that performs **quantum-mechanical
molecular dynamics** (MD). This benchmark requires low latency networks to
scale (Infiband over GbE). A free software stack
(Open64/MVAPICH2/ACML/ScaLAPACK) performs comparably to Intel stack.

## Changes

| Version | Date       | Author           | Notes                             |
| ------- | ---------- | ---------------- | --------------------------------- |
| 0.1.1   | 2020-05-03 | Christian Külker | Typos                             |
| 0.1.0   | 2016-08-27 | Christian Külker | Initial release                   |

## VASP on Bright Cluster Manager

### First Time Preparation

As root

```shell
cd /root/ckuelker/bm/bin
./speedup
./init-intel-license
./init-intel-compiler

cd /home/hpc/bm-vasp/src/Benchmarks
tar xvzf vasp.tar.gz
mv vasp vasp-007
chown -R hpc:hpc vasp-007
```

### Before Every Run

As user `hpc`:

```shell
source /cm/shared/apps/intel-ics-2013/bin/compilervars.sh intel64
source /cm/shared/apps/intel-ics-2013/bin/iccvars.sh intel64
source /cm/shared/apps/intel-ics-2013/bin/ifortvars.sh intel64
source /cm/shared/apps/intel-ics-2013/impi/4.1.0.024/intel64/bin/mpivars.sh intel64
source /cm/shared/apps/intel-ics-2013/composer_xe_2013.1.117/mkl/bin/mklvars.sh intel64
```

### Build The Benchmark

```shell
cd /home/hpc/bm-vasp/src/Benchmarks/vasp-007
./build.sh
```

### Run The Benchmark

```shell
cd /home/hpc/bm-vasp/src/Benchmarks/vasp-007
./run.sh
```

Eventually edit `run.sh` and change core number to be used.

### Optimization

Optimization (changing compiler flags) in the make files:

```shell
Benchmarks/vasp/src/vasp.5.lib/Makefile
Benchmarks/vasp/src/wannier90-1.2/Makefile
Benchmarks/vasp/src/vasp.5.2.12/makefile
```

## Example Session Debugging On Intel Cluster

The Intel cluster has 12 core per CPU, Intel(R) Xeon(R) CPU E5645 @ 2.40GHz.
Assuming the source is in `~/bm/src/vasp.tar.gz`

```shell
tar xvzf bm/src/vasp.tar.gz
cd bm/src/vasp-5.2.12
module load intel/cs-xe-2013--binary
module load intelmpi/4.1.1--binary
module load blas/2011--intel--cs-xe-2013--binary
#module load boost/1.53.0--intel--cs-xe-2013--binary
```

One problem is that the Intel wrapper is not made available for VASP. The file
`/lib/intel64/libfftw3xf_intel.a` do not exists. Which will be announced like
this:

```
make: *** No rule to make target `/lib/intel64/libfftw3xf_intel.a', needed by
`vasp'.  Stop.

```

### Solving

```shell
mkdir -p  ~/bm/build/intel/fftw3xf
cd /prod/compilers/intel/cs-xe-2013/binary/composer_xe_2013.1.117/mkl/interfaces/fftw3xf
make libem64t compiler=intel install_to=~/bm/build/intel/fftw3xf

echo "export LD_LIBRARY_PATH=/new/prod/compilers/intel/cs-xe-2013/binary/impi/4.1.1.036/lib64/:/new/prod/compilers/intel/cs-xe-2013/binary/lib/intel64/:~/bm/build/intel/fftw3xf" > env
source env
```

That do not work, try this

```shell
cp src/vasp.5.2.12/makefile src/vasp.5.2.12/makefile.original
sed -i -e 's%\$(MKL_PATH)/libfftw3xf_intel.a%~/bm/build/intel/fftw3xf/libfftw3xf_intel.a%' src/vasp.5.2.12/makefile
diff src/vasp.5.2.12/makefile.original src/vasp.5.2.12/makefile                                                                                                   <9:51
73c73
< FFT3D   = fftmpi.o fftmpi_map.o  fftw3d.o  fft3dlib.o  $(MKL_PATH)/libfftw3xf_intel.a
---
> FFT3D   = fftmpi.o fftmpi_map.o  fftw3d.o  fft3dlib.o  ~/bm/build/intel/fftw3xf/libfftw3xf_intel.a
```

Then an other error occurs

```
ifort: error #10236: File not found:  '/opt/intel/mkl/lib/intel64/libmkl_scalapack_lp64.a'
ifort: error #10236: File not found:  '/opt/intel/mkl/lib/intel64/libmkl_blacs_intelmpi_lp64.a'
```

They can be found at:

`/prod/compilers/intel/cs-xe-2013/binary/composer_xe_2013.1.117/mkl/lib/intel64/`

```shell
sed -i -e 's%/opt/intel/mkl/lib/intel64/libmkl_blacs_intelmpi_lp64.a%/prod/compilers/intel/cs-xe-2013/binary/composer_xe_2013.1.117/mkl/lib/intel64/libmkl_blacs_intelmpi_lp64.a%' src/vasp.5.2.12/makefile

sed -i -e 's%/opt/intel/mkl/lib/intel64/libmkl_scalapack_lp64.a%/eurora_old/prod/compilers/intel/cs-xe-2013/binary/composer_xe_2013.1.117/mkl/lib/intel64/libmkl_scalapack_lp64.a%' src/vasp.5.2.12/makefile
```

## Links

* [VASP Analysis and Profiling](https://www.hpcadvisorycouncil.com/pdf/VASP_Analysis_and_Profiling_AMD.pdf)


