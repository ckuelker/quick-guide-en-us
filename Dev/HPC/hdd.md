---
title: Simple HDD/SSD Benchmarks
linkTitle: HDD/SDD
author: Christian Külker
date: 2017-10-25
version: 0.1.0
type: doc
disclaimer: True
TOC: True
categories:
- HPC
- Benchmark
tags:
- HDD
- SDD
- hdparm
- iozone
- Gnome Disk Utility
- smartctl
- SATA
description: Simple HDD/SSD benchmarks with Linux tools

---

## Introduction

This document introduces some utilities and test to help to understand
the performance of Hard Disk Drives (HDD), Solid State Drives (SSD), and
others.

### History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2022-05-24 | +Introduction, +History                              |
| 0.1.0   | 2017-10-25 | Initial release                                      |

## Tools

```
smartctl
hdparm
bonnie++
gnome-disk-utility
dd
iozone
```

## Hints

Make sure the device interface (SATA/SAS/mSATA/M.2/NVMe) of the host system is
the best that the disk can support. See `smartctl` for more info: SATA Version
is:  SATA 3.1, 6.0 Gb/s (2017: 3.0 Gb/s)

## Gnome Disk Utility

```bash
aptitude install gnome-disk-utility
/usr/bin/gnome-disks
```

1. Select disk, Push cog button, select SMART Data and Self-Tests
    "Start Self-Test": short, long  and conveyance.

2. Select disk, Push cog button, select Benchmark

```
   Transfer Rate:
       Number of Samples: 100
       Sample Size: 10 MiB (10,485,760 bytes)
    Access Time:
       Number of Samples: 1000
```

Make screenshot

## hdparm

```bash
for i in {1..10}; do hdparm -t /dev/sdb > hdparm-t-$i.log;done
for i in {1..10}; do hdparm -t /dev/sdb > hdparm-T-$i.log;done
```

Use a spread sheet and `psppire` to analyse.

## smartctl

```bash
smartctl -a /dev/sdb > smartctl-a.log
smartctl -i /dev/sdb > smartctl-i.log
smartctl -x /dev/sdb > smartctl-x.log
```

## Further Reading

- <https://wiki.archlinux.org/index.php/Benchmarking>

### smartctl

- <https://www.thomas-krenn.com/de/wiki/SMART_Tests_mit_smartctl>
- <https://www.thomas-krenn.com/de/wiki/Analyse_einer_fehlerhaften_Festplatte_mit_smartctl>


