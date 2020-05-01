---
title: Taskset
type: doc
author: Christian Külker
date: 2020-05-01
keywords:
    - HPC
    - taskset
categories:
    - HPC
tags:
    - taskset
    - numctl

---

The command `taskset` is used to set or get a process's CPU affinity. CPU
affinity is a feature that allows you to bind or unbind processes to a
particular central processing unit, or a range of CPUs.

## Installation

```shell
aptitude install util-linux
```

## Usage

### How To Get CPU Affinity Of A Process?

Use the **PID** with the `-p` option to get the affinity mask.

```shell
taskset -p 32677
pid 32677's current affinity mask: ff
```

The mask is a bit mask. For this example the process has the affinity to run on
all CPUs hex `0xff` = binary `11111111` = decimal 8 cores. That the system has
8 cores can easy confirmed with [numctl](./numa.html)

```shell
numactl --hardware
available: 1 nodes (0)
node 0 cpus: 0 1 2 3 4 5 6 7
node 0 size: 11975 MB
node 0 free: 3123 MB
node distances:
node   0
  0:  10
```

## How To Change The CPU Affinity Of A Process?

```shell
taskset -p  32677
pid 32677's current affinity mask: ff
taskset -p 0x0f 32677
pid 32677's current affinity mask: ff
pid 32677's new affinity mask: f
taskset -p  32677
pid 32677's current affinity mask: f
```

## How To Start A Process With CPU Affinity?

This task is quite common in HPC, when starting a new process manually or via a
scheduler one should know and understand how to pin a process to certain CPU's

```shell
taskset 0xf0 long-running-command
```

It is of course also possible to run a process on a **range** of CPU's with the
`-cp` option, but that usually makes only sense if the task switching has no
penalty involved. Usually it is better to pin a process to one core unless the
task is a wrapper that pins it sub process by itself to dedicated cores.
