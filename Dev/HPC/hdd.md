---
title: Simple Hdd Benchmarks
linkTitle: Hdd
author: Christian Külker
date: 2017-10-25
version: 0.1.0
type: doc
disclaimer: True
toc: True
categories:
- HPC
- Benchmark
tags:
- Hdd
- hdparm
- iozone
- Gnome Disk Utility
- smartctl
- SATA
description: Simple Hdd Benchmarks With Linux Tools

---

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

Make sure the device (SATA) of the host system is the best that the disk can
support. See `smartctl` for more info: SATA Version is:  SATA 3.1, 6.0 Gb/s
(current: 3.0 Gb/s)

## Gnome Disk Utility

```shell
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

```shell
for i in {1..10}; do hdparm -t /dev/sdb > hdparm-t-$i.log;done
for i in {1..10}; do hdparm -t /dev/sdb > hdparm-T-$i.log;done
```

Use a spread sheet and `psppire` to analyse.

## smartctl

```shell
smartctl -a /dev/sdb > smartctl-a.log
smartctl -i /dev/sdb > smartctl-i.log
smartctl -x /dev/sdb > smartctl-x.log
```

## Further Reading

https://wiki.archlinux.org/index.php/Benchmarking

### smartctl

https://www.thomas-krenn.com/de/wiki/SMART_Tests_mit_smartctl
https://www.thomas-krenn.com/de/wiki/Analyse_einer_fehlerhaften_Festplatte_mit_smartctl


