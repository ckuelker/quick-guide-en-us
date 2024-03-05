---
title: MpStat
author: Christian Külker
date: 2024-03-05
version: 0.1.0
type: doc
disclaimer: True
toc: True
pdf: True
categories:
- Monitoring
- Admin
commands:
- mpstat
tags:
- Sysstat
- MpStat
description: Reports CPU activity statistics

---

## Introduction

The `mpstat` command, part of the `sysstat` package like `iostat`, is a
tool designed for monitoring CPU activity in Linux systems. It can be
used on both single processor (UP) and multi-processor (SMP) machines. By
default, `mpstat` displays CPU utilization reports, and if no specific activity
is selected, it provides the global average CPU usage since the system's
startup. The utility is capable of reporting detailed statistics for
each processor or for the processors as a group.

## Installation

As with `iostat`, the installation of `mpstat` is straightforward:

```bash
aptitude install sysstat
```

## Usage

Executing `mpstat` without any parameters provides a snapshot of the CPU
activities across all processors since boot. The command can be customized with
various parameters to alter its output for more specific monitoring needs.

__Basic Command Usage:__

- __Interval and Count:__ Specifying an interval and count (in seconds), such
  as `mpstat 2 5`, will result in the command producing CPU activity reports
every two seconds, five times in total and calculate the average.

```bash
mpstat  2 5
Linux 6.1.0-18-amd64 (v2)     03/05/2024     _x86_64_    (4 CPU)

01:22:02 PM CPU  %usr %nice %sys %iowait %irq %soft %steal %guest %gnice %idle
01:22:04 PM all 20.97  0.00 5.99    0.00 0.00  1.87   0.00   0.00   0.00 71.16
01:22:06 PM all 21.12  0.00 5.98    0.00 0.00  1.53   0.00   0.00   0.00 71.37
01:22:08 PM all 24.17  0.00 5.98    0.13 0.00  0.89   0.00   0.00   0.00 68.83
01:22:10 PM all 22.26  0.00 5.53    0.00 0.00  1.51   0.00   0.00   0.00 70.69
01:22:12 PM all 24.34  0.00 5.14    0.00 0.00  1.63   0.00   0.00   0.00 68.88
Average:    all 22.57  0.00 5.73    0.03 0.00  1.49   0.00   0.00   0.00 70.19
```

- __Processor Specific Statistics:__ Using `-P` followed by a processor number
  (e.g., `-P 1` for the first processor) or `ALL` (e.g., `-P ALL`), you can
view statistics for specific processors or for all processors, respectively.

```bash
mpstat -P ALL
Linux 6.1.0-18-amd64 (v2)     03/05/2024     _x86_64_    (4 CPU)

01:25:40 PM CPU  %usr %nice %sys %iowait %irq %soft %steal %guest %gnice %idle
01:25:40 PM all 17.14  0.00 5.06    0.03 0.00  0.81   0.00   0.28   0.00 76.67
01:25:40 PM   0 17.38  0.00 4.98    0.03 0.00  0.91   0.00   0.26   0.00 76.46
01:25:40 PM   1 17.28  0.00 4.84    0.03 0.00  1.32   0.00   0.31   0.00 76.22
01:25:40 PM   2 17.38  0.00 4.97    0.03 0.00  0.61   0.00   0.33   0.00 76.69
01:25:40 PM   3 16.53  0.00 5.47    0.03 0.00  0.42   0.00   0.24   0.00 77.32
```


- __Interrupts Statistics:__ The `-I` option (e.g., `-I SCPU`, `-I CPU`, `-I
  SUM`, or `-I ALL`) is used for reporting different types of interrupts
statistics per second by CPU.

```bash
mpstat -I SUM
Linux 6.1.0-18-amd64 (v2)     03/05/2024     _x86_64_    (4 CPU)

01:26:57 PM  CPU    intr/s
01:26:57 PM  all   2164.37
```

```bash
mpstat -I ALL
Linux 6.1.0-18-amd64 (v2)     03/05/2024     _x86_64_    (4 CPU)

01:41:52 PM  CPU    intr/s
01:41:52 PM  all   2167.22

01:41:52 PM  CPU        0/s        1/s        4/s        8/s        9/s ...
01:41:52 PM    0       0.00       0.00       0.00       0.00       0.00 ...
01:41:52 PM    1       0.00       0.00       0.00       0.00       0.00 ...
01:41:52 PM    2       0.00       0.00       0.00       0.00       0.00 ...
01:41:52 PM    3       0.00       0.00       0.00       0.00       0.00 ...

01:41:52 PM  CPU       HI/s    TIMER/s   NET_TX/s   NET_RX/s    BLOCK/s ...
01:41:52 PM    0       0.05      18.67       0.01     149.59       0.00 ...
01:41:52 PM    1     416.58      16.47       0.00       0.30       0.00 ...
01:41:52 PM    2       0.04      27.01       0.00       0.35       6.51 ...
01:41:52 PM    3       0.03      17.14       0.00       0.32       0.00 ...
```
__Advanced Usage:__

1. __JSON Output:__ For programmatically parsing the output, the `-o JSON`
  option formats the statistics in JSON (JavaScript Object Notation).
  See [IoStat](iostat.md) for ideas on how to use this format.

2. __Utilization of Specific Processors:__ To focus on particular processors,
use `mpstat -P 0,2,3` specifying the processors of interest.

```bash
mpstat -P 0,2,3
Linux 6.1.0-18-amd64 (v2)     03/05/2024     _x86_64_    (4 CPU)

01:41:12 PM CPU  %usr %nice  %sys %iowait %irq %soft %steal %guest %gnice %idle
01:41:12 PM   0 17.39  0.00  4.98    0.03 0.00  0.91   0.00   0.26   0.00 76.44
01:41:12 PM   2 17.38  0.00  4.97    0.03 0.00  0.61   0.00   0.33   0.00 76.69
01:41:12 PM   3 16.53  0.00  5.47    0.03 0.00  0.42   0.00   0.24   0.00 77.32
```
3. __Reporting with NUMA Node Placement:__ Employ `mpstat -N ALL` for
statistics based on NUMA node placement.

```bash
mpstat -P 0,2,3 -N ALL
Linux 6.1.0-18-amd64 (v2)     03/05/2024     _x86_64_    (4 CPU)

01:40:28 PM CPU  %usr %nice %sys %iowait %irq %soft %steal %guest %gnice %idle
01:40:28 PM   0 17.38  0.00 4.98    0.03 0.00  0.91   0.00   0.26   0.00 76.44
01:40:28 PM   2 17.38  0.00 4.97    0.03 0.00  0.61   0.00   0.33   0.00 76.69
01:40:28 PM   3 16.53  0.00 5.47    0.03 0.00  0.42   0.00   0.24   0.00 77.32

01:40:28 PM NODE  %usr %nice %sys %iowait %irq %soft %steal %guest %gnice %idle
01:40:28 PM  all 17.15  0.00 5.06    0.03 0.00  0.81   0.00   0.28   0.00 76.66
01:40:28 PM    0 17.15  0.00 5.06    0.03 0.00  0.81   0.00   0.28   0.00 76.66
```

## Links

- Home page <https://sysstat.github.io/>
- Source <https://github.com/sysstat/sysstat>

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2024-03-05 | Initial release                                      |





