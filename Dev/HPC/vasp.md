---
title: VASP Benchmark
linkTitle: VASP
author: Christian Külker
date: 2022-06-14
version: 0.1.3
type: doc
disclaimer: True
TOC: True
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
(Open64/MVAPICH2/ACML/ScaLAPACK) performs comparably to the Intel stack.

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.3   | 2022-06-14 | Shell->bash, changes->history, typos                 |
| 0.1.2   | 2020-12-27 | Improve link section, line length                    |
| 0.1.1   | 2020-05-03 | Typos                                                |
| 0.1.0   | 2016-08-27 | Initial release                                      |

## VASP on Bright Cluster Manager

### First Time Preparation

As root

```bash
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

```bash
source /cm/shared/apps/intel-ics-2013/bin/compilervars.sh intel64
source /cm/shared/apps/intel-ics-2013/bin/iccvars.sh intel64
source /cm/shared/apps/intel-ics-2013/bin/ifortvars.sh intel64
source /cm/shared/apps/intel-ics-2013/impi/4.1.0.024/intel64/bin/mpivars.sh intel64
source /cm/shared/apps/intel-ics-2013/composer_xe_2013.1.117/mkl/bin/mklvars.sh intel64
```

### Build The Benchmark

```bash
cd /home/hpc/bm-vasp/src/Benchmarks/vasp-007
./build.sh
```

### Run The Benchmark

```bash
cd /home/hpc/bm-vasp/src/Benchmarks/vasp-007
./run.sh
```

Eventually edit `run.sh` and change core number to be used.

### Optimization

Optimization (changing compiler flags) in the make files:

```bash
Benchmarks/vasp/src/vasp.5.lib/Makefile
Benchmarks/vasp/src/wannier90-1.2/Makefile
Benchmarks/vasp/src/vasp.5.2.12/makefile
```

## Example Session Debugging On Intel Cluster

The Intel cluster has 12 core per CPU, Intel(R) Xeon(R) CPU E5645 @ 2.40GHz.
Assuming the source is in `~/bm/src/vasp.tar.gz`

```bash
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

```bash
mkdir -p  ~/bm/build/intel/fftw3xf
cd /prod/compilers/intel/cs-xe-2013/binary/composer_xe_2013.1.117/mkl/\
interfaces/fftw3xf
make libem64t compiler=intel install_to=~/bm/build/intel/fftw3xf

echo "export LD_LIBRARY_PATH=/new/prod/compilers/intel/cs-xe-2013/binary/impi\
/4.1.1.036/lib64/:/new/prod/compilers/intel/cs-xe-2013/binary/lib/intel64/:~/\
bm/build/intel/fftw3xf" > env
source env
```

That do not work, try this

```bash
cp src/vasp.5.2.12/makefile src/vasp.5.2.12/makefile.original
sed -i -e 's%\$(MKL_PATH)/libfftw3xf_intel.a%~/bm/build/intel/fftw3xf/\
libfftw3xf_intel.a%' src/vasp.5.2.12/makefile
diff src/vasp.5.2.12/makefile.original src/vasp.5.2.12/makefile
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

```bash
sed -i -e 's%/opt/intel/mkl/lib/intel64/libmkl_blacs_intelmpi_lp64.a%/prod/\
compilers/intel/cs-xe-2013/binary/composer_xe_2013.1.117/mkl/lib/intel64/\
libmkl_blacs_intelmpi_lp64.a%' src/vasp.5.2.12/makefile

sed -i -e 's%/opt/intel/mkl/lib/intel64/libmkl_scalapack_lp64.a%/eurora_old/\
prod/compilers/intel/cs-xe-2013/binary/composer_xe_2013.1.117/mkl/lib/intel64/\
libmkl_scalapack_lp64.a%' src/vasp.5.2.12/makefile
```

## Warnings

### ifort - manual

Suppressing warning and remarks:

Option `-diag-disable warn` (Linux and Mac OS X) and `/Qdiag-disable:warn`
(Windows) disable all Source Checker diagnostics except those that have an
"error" severity level. They suppress all Source Checker warnings, cautions,
and remarks.

## Links

### VASP Analysis and Profiling

The original link to a
[PDF](https://www.hpcadvisorycouncil.com/pdf/VASP_Analysis_and_Profiling_AMD.pdf)
from slides of 2013 should be available. However sometimes (2020-10-21) are
not. In this case the document is available via
[archive.org](https://web.archive.org/web/20190419065437/http://hpcadvisorycouncil.com/pdf/VASP_Analysis_and_Profiling_AMD.pdf)

### Installation, Compiling

- [VASP 5.3.5](https://wiki.bath.ac.uk/pages/viewpage.action?pageId=85075554)
- [VASP 5.x.x](https://www.vasp.at/wiki/index.php/Installing_VASP.5.X.X)
- [VASp 6.x.x](https://www.vasp.at/wiki/index.php/Installing_VASP.6.X.X)
- [with Intel Composer XE 12.1.3 and OpenMP](https://www.nsc.liu.se/~pla/vaspcompile/)

### Intel MKL link advisor

- [Advisor v6.13](https://software.intel.com/en-us/articles/intel-mkl-link-line-advisor/)

### Performance Optimisation

- Hardware optimization
  [Softpanorama](http://www.softpanorama.org/HPC/Molecular_modeling_software/Vasp/vasp_performance.shtml)
- GCC and profiling
  [ivofilot.nl](http://www.ivofilot.nl/posts/view/20/Optimizing+VASP+performance+by+tuning+the+compilation+using+the+GNU+compilers)
- [vasp.at](https://www.vasp.at/vasp-workshop/slides/performance.pdf) backup at
  [archive.org](https://web.archive.org/web/20180903073934/https://www.vasp.at/vasp-workshop/slides/performance.pdf)

