---
linkTitle: NUMA
title: Non-Uniform Memory Access
type: doc
author: Christian Külker
date: 2020-05-01
version: 0.1.1
disclaimer: True
toc: True
keywords:
    - NUMA
    - HPC
categories:
    - HPC
tags:
    - numactl
    - numademo
    - numastat
    - memhog
    - migspeed
    - migratepages

---

The Non-Uniform Memory Access (NUMA) is a design specification of some modern
multiprocessing architectures that are characterized by the fact that not all
memory can be access equally from all CPUs. Usually the is due to the fact
that each CPU has some memory attached to it. That do not necessarily mean that
this memory can not be access by process from other CPUs, but accessing the
memory has some performance penalty. As creating a NUMA architecture is cheaper
but still performs well if the programs is carefully designed, this design 
is quite popular in High Performance Computing (HPC).

## Changes

| Version | Date       | Author           | Notes                             |
| ------- | ---------- | ---------------- | --------------------------------- |
| 0.1.1   | 2020-05-01 | Christian Külker | update for Debian Buster          |
| 0.1.0   | 2016-03-24 | Christian Külker | initial release                   |

## Installing

The content of installation varies. It is recommended to either
use a new distribution or even compile **numactl** from source,
as this also includes the `numademo` command.

## Installing NUMA Helper Tools Debian 9.12 Strech

* migratepages
* numactl
* numastat

```shell
aptitude install numactl
```

## Install NUMA Helper Tools Debian 10 Buster

* memhog
* migratepages
* migspeed
* numactl
* numastat

```shell
aptitude install numactl
```

However, `numademo` is missing.

## Compiling And Installing From Source

```shell
git clone https://github.com/numactl/numactl.git
cd numctl
./autogen.sh
./configure
make
make install
```

## Commands

### migratepages

The man pages says: "migratepages  moves  the  physical  location of a
processes pages without any changes of the virtual address space of the
process. Moving the pages allows one to change the distances of a process to
its memory. Performance may be optimized by moving a processes pages to the
node where it is executing."

### numastat

The `numastat` command shows per-NUMA statistics for processes and the
operating system.

Example for one CPU:

```
numastat
                               node0
    numa_hit                  366309
    numa_miss                      0
    numa_foreign                   0
    interleave_hit              7846
    local_node                366309
    other_node                     0
```

### numactl

The `numactl` command controls the NUMA policy for processes or shared memory.


Example for one CPU:

```
    numactl --show
    policy: default
    preferred node: current
    physcpubind: 0 1 2 3 4 5
    cpubind: 0
    nodebind: 0
    membind: 0

    numactl --hardware
    available: 1 nodes (0-0)
    node 0 size: 6025 MB
    node 0 free: 5612 MB
    node distances:
    node   0
      0:  10
```

### memhog

The `memhog` command allocates memory with a policy for testing.  For some
reasons the Debian 10 Buster version do not include a man page.  However there
is a [page](http://man7.org/linux/man-pages/man8/memhog.8.html) online.

Allocate a 1G region, (implicit) default policy, repeat test 4 times
```
memhog -r4 1G
...............................................................................
...............................................................................
...............................................................................
...............................................................................
```

### numademo

On an old laptop from 2008

```shell
./numademo -S 100M
1 nodes available
memory with no policy memset              Avg 2304.98 MB/s Max 2310.25 MB/s ...
local memory memset                       Avg 2302.23 MB/s Max 2310.05 MB/s ...
memory interleaved on all nodes memset    Avg 2295.28 MB/s Max 2307.35 MB/s ...
memory on node 0 memset                   Avg 2303.58 MB/s Max 2306.69 MB/s ...
...

```

## Further Reading

* [NUMA - hpc wiki info](https://hpc-wiki.info/hpc/NUMA)
* [NUMA - wikipedia](https://en.wikipedia.org/wiki/Non-uniform_memory_access)
* [github](https://github.com/numactl/numactl)


