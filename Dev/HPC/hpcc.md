---
linkTitle: HPCC
title: HPC Challenge
author: Christian Külker
date: 2023-01-17 
version: 0.1.3
type: doc
disclaimer: True
toc: True
locale: en_US
lang: en
categories:
- HPC
- Benchmark
tags:
- Hpcc
description: HPC Challenge Benchmark

---

The **HPC Challenge** (HPCC) is a benchmark suite that measures a range of
memory access patterns.

The **HPCC** consists of seven tests:

1. High Performance Linpak (HPL)
2. DGEMM
3. STREAM
4. PTRANS
5. RandomAccess
6. FFT
7. Communication bandwidth and latency

## Download Version 1.4.3

```bash
cd /srv/src
wget http://icl.cs.utk.edu/projectsfiles/hpcc/download/hpcc-1.4.3.tar.gz
cd /srv/build
tar xvzf ../src/pcc-1.4.3.tar.gz
```

## Download Version 1.5.0

```bash
cd /srv/src
wget http://icl.cs.utk.edu/projectsfiles/hpcc/download/hpcc-1.5.0.tar.gz
cd /srv/build
tar xvzf ../src/pcc-1.5.0.tar.gz
```

## Compile 1.4.3 On an Intel Cluster

To compile the code under a fictive architecture **ArchitectureE52697v2** the
Makefile needs to be copied and edited.

```bash
cd /srv/build/hpcc-1.4.5
module load mkl/intel
module load intel/15.0
module load impi/5.0.1035
cp Make.SOMETHING hpl/Make.ArchitectureE52697v2
make ArchitectureE52697v2

```

## Links

* [HPC Challange Benchmark - Home](https://icl.utk.edu/hpcc/)
* [Source Code - Source Forge](https://sourceforge.net/projects/hpcc/)

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.3   | 2023-01-17 | Add locale, lang to front matter                     |
| 0.1.2   | 2022-06-13 | Shell->bash, changes->history                        |
| 0.1.1   | 2016-03-20 | version 1.5.0                                        |
| 0.1.0   | 2016-02-01 | Initial release                                      |
