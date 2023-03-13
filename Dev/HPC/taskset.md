---
title: Taskset
type: doc
author: Christian Külker
date: 2023-03-13
version: 0.1.2
disclaimer: True
toc: True
keywords:
    - HPC
    - taskset
categories:
    - HPC
tags:
    - taskset
    - numactl
commands:
    - taskset
    - numactl
packages:
    - util-linux


---

The `taskset` command is used to set or get the CPU affinity of a process. CPU
affinity is a feature that allows you to bind or unbind processes to a
particular CPU or range of CPUs.

## Installation

```bash
aptitude install util-linux
```
## Usage

### How Do I Get the CPU Affinity of a Process?

Use the **PID** with the `-p` option to get the affinity mask.

```bash
taskset -p 32677
pid 32677's current affinity mask: ff
```

The mask is a bit mask. For this example, the process has the affinity to run
on all CPUs hex `0xff` = binary `11111111` = decimal 8 cores. That the system
has 8 cores can be easily confirmed with [numctl](numa.md).

```bash
numactl --hardware
available: 1 nodes (0)
node 0 cpus: 0 1 2 3 4 5 6 7
node 0 size: 11975 MB
node 0 free: 3123 MB
node distances:
node   0
  0:  10
```

## How to Change the CPU Affinity Of a Process?

```bash
taskset -p  32677
pid 32677's current affinity mask: ff
taskset -p 0x0f 32677
pid 32677's current affinity mask: ff
pid 32677's new affinity mask: f
taskset -p  32677
pid 32677's current affinity mask: f
```

## How to Start a Process with CPU Affinity?

This task is quite common in HPC, so when starting a new process manually or
through a scheduler, you should know and understand how to pin a process to
specific CPUs.

```bash
taskset 0xf0 long-running-command
```

Of course, it is also possible to run a process on a **range** of CPUs with the
`-cp` option, but this usually only makes sense if there is no penalty for
switching tasks. It is usually better to pin a process to one core, unless the
task is a wrapper that pins its subprocess to dedicated cores by itself.


## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2023-03-13 | Improve writing, fix version, fix link               |
| 0.1.1   | 2022-06-15 | History, shell->bash                                 |
| 0.1.0   | 2020-05-01 | Initial release                                      |


