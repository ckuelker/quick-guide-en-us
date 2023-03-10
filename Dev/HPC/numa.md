---
linkTitle: NUMA
title: Non-Uniform Memory Access
type: doc
author: Christian Külker
date: 2023-03-10
version: 0.1.3
disclaimer: True
toc: True
keywords:
    - NUMA
    - HPC
categories:
    - HPC
tags:
    - lscpu
    - numactl
    - numademo
    - numastat
    - numatop
    - memhog
    - migratepages
    - migspeed
    - util-linux
commands:
    - lscpu
    - numactl
    - numademo
    - numastat
    - numatop
    - memhog
    - migratepages
    - migspeed
description: Usage of Non-Uniform Memory Access

---

Non-uniform memory access ([NUMA]) is a design specification of some modern
multiprocessing architectures that, unlike uniform memory access ([UMA]), does
not allow all CPUs to access all memory equally. Usually this is due to the
fact that each CPU has some memory attached to it. This does not necessarily
mean that this memory cannot be accessed by processes from other CPUs, but
accessing the memory has some performance penalty. Because building a [NUMA]
architecture is cheaper and still performs well if the programs are carefully
designed, this design is quite popular in high performance computing ([HPC]).

<!--
~~~
UMA:                       NUMA:

     ---++---------++---           ---++---------++---
        ||         ||                 ||         ||
     +------+   +------+           +------+   +------+
     | CPU0 |   | CPU1 |           | CPU0 |   | CPU1 |
     +------+   +------+           +------+   +------+
        ||         ||                 ||         ||
     ---++---------++---           +------+   +------+
        ||         ||              | MEM0 |   | MEM1 |
     +------+   +------+           +------+   +------+
     | MEM0 |   | MEM1 |
     +------+   +------+
~~~
-->

<!--
~~~
──────────────────┬─────────────────── UMA
         ╭──────╮ │ ╭──────╮
         │ CPU1 ├─┴─┤ CPU1 │
         ╰───╥──╯   ╰──╥───╯
             ╠═════════╣
         ╭───╨──╮   ╭──╨───╮
         │ MEM0 │   │ MEM1 │
         ╰──────╯   ╰──────╯

──────────────────┬─────────────────── NUMA
╭──────╮ ╭──────╮ │ ╭──────╮ ╭──────╮
│ MEM0 ╞═╡ CPU1 ├─┴─┤ CPU1 ╞═╡ MEM1 │
╰──────╯ ╰──────╯   ╰──────╯ ╰──────╯
~~~
-->

![UMA](uma-v0.1.0.png)

![NUMA](numa-v0.1.0.png)

## Installing

The content of the installation varies. It is recommended to either use a new
distribution or even compile __numactl__ from source, as this includes the
`numademo` command.

## Installing Numa Helper Tools For Debian

| Package      | Stretch 9.12    | Buster 10      | Bullseye 11      |
| -------------|---------------- | -------------- | ---------------- |
| numactl      | 2.0.11-2.1      | 2.0.12-1       | 2.12-1+b1        |
| numad        | 0.5+20150602-5  | 0.5+20150602-5 | 0.5+20150602-7   |
| numatop      | 1.0.4-3         | 2.1-2          | 2.1-4            |
| util-linux   | 2.29.2-1+deb9u1 | 2.33.1-0.1     | 2.36.1-8+deb11u1 |


| Command      | Package    | Source     |
| ------------ | ---------- | ---------- |
| lscpu        | util-linux | util-linux |
| memhog       | numactl    | numactl    |
| numastat     | numactl    | numactl    |
| migspeed     | numcatl    | numactl    |
| migratepages | numactl    | numactl    |
| numademo     | n.a.       | numactl    |
| numatop      | numatop    | numatop    |
| numad        | numad      | numad      |


- `numademo` is part of `numactl` but not packaged

## lscpu

The tool __lscpu__ can be used to understand the number of [NUMA] nodes.

```bash
  # Laptop 2008
lscpu|grep NUMA
NUMA node(s):          1
NUMA node0 CPU(s):     0,1
  # Desktop 2015
lscpu|grep NUMA
NUMA node(s):          1
NUMA node0 CPU(s):     0-7
  # Raspberry Pi 4
lscpu|grep NUMA
NUMA node(s):                    1
NUMA node0 CPU(s):               0-3
  # Server 2022
lscpu|grep NUMA
NUMA node(s):                    1
NUMA node0 CPU(s):               0-11
```

Standard hardware not used for [HPC] typically has only one [NUMA] node.
Typical `X86` [NUMA] hardware has 2 or more CPUs and 2 or more memory banks,
one attached to each CPU.

## Compiling And Installing numactl From Source

```bash
aptitude install autoconf automake
git clone https://github.com/numactl/numactl.git
cd numctl
./autogen.sh
./configure
make
make install
```

## Numactl Commands

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

```bash
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

The `numactl` command controls the [NUMA] policy for processes or shared
memory.

Example for one CPU:

```bash
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
reason, the Debian 10 Buster release does not include a man page.  However,
there is a [page] online (http://man7.org/linux/man-pages/man8/memhog.8.html).

Allocate a 1G region, (implicit) default policy, repeat test 4 times

```bash
memhog -r4 1G
...............................................................................
...............................................................................
...............................................................................
...............................................................................
```

### numademo

The `numademo` command is not available as a Debian package, it is available as
`numctl` source code.

On an old laptop from 2008 with Debian 8.11 Jessie (used with a compiled
`numademo` executable from `numactl` source from before 2020, maybe in 2016):

```bash
./numademo -S 100M
1 nodes available
memory with no policy memset              Avg 2304.98 MB/s Max 2310.25 MB/s ...
local memory memset                       Avg 2302.23 MB/s Max 2310.05 MB/s ...
memory interleaved on all nodes memset    Avg 2295.28 MB/s Max 2307.35 MB/s ...
memory on node 0 memset                   Avg 2303.58 MB/s Max 2306.69 MB/s ...
[...]
```

Running this test on Debian 11 Bullseye on a 2013 desktop or 2015 laptop, or on
the same 2008 laptop (Debian 8.11 Jessie) the newer version gives:

```bash
/numademo -S 100M
A minimum of 2 nodes is required for this test.
```

It seems that the minimum requirement for the test changed.

## Numatop

This utility requires a supported CPU. If executed on an unsupported CPU, it
will indicate that:

```bash
numatop -s low -l 2 -f /tmp/warn.log
CPU is not supported!
```

## Further Reading

* [NUMA - hpc wiki info](https://hpc-wiki.info/hpc/NUMA)
* [NUMA - wikipedia](https://en.wikipedia.org/wiki/Non-uniform_memory_access)
* [github](https://github.com/numactl/numactl)

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.3   | 2023-03-10 | Improve writing, move history                        |
| 0.1.2   | 2022-05-17 | Change shell blocks to bash block, history, dots     |
|         |            | description, Debian helper tools table, +lscpu       |
|         |            | Update for Debian 11 Bullseye                        |
| 0.1.1   | 2020-05-01 | Update for Debian 10 Buster                          |
| 0.1.0   | 2016-03-24 | Initial release                                      |


[HPC]: https://en.wikipedia.org/wiki/High-performance_computing
[NUMA]: https://en.wikipedia.org/wiki/Non-uniform_memory_access
[UMA]: https://en.wikipedia.org/wiki/Uniform_memory_access
