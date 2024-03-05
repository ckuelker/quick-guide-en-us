---
title: SysStat
author: Christian Külker
version: 0.1.1
date: 2024-03-05
type: doc
locale: en_US
lang: en
disclaimer: True
toc: True
categories:
- Minitoring
- Admin
- Benchmarking
commands:
- ls
tags:
- sysstat
- iostat
- mpstat
discription: System performance tools for the Linux operating system

---

## Introduction

The SysStat package, authored by Sebastian Godard and licensed under GPLv2, is
supported by 58 contributors on GitHub. This suite comprises several utilities
aimed at monitoring system performance and usage:

- [__iostat__]  : Delivers CPU and input/output statistics for block devices
                  and partitions.
- [__mpstat__]  : Provides statistics related to individual or multiple
                  processors.
- __pidstat__   : Offers statistics for Linux tasks (processes), encompassing
                  I/O, CPU, memory, and more.
- __tapestat__  : Supplies statistics for tape drives connected to the system.
- __cifsiostat__: Generates CIFS statistics.

[__iostat__]: iostat.md
[__mpstat__]: mpstat.md

SysStat includes several tools intended for scheduling via cron or `systemd` to
accumulate and record performance and activity data:

- __sar__:   Gathers, reports, and stores system activity information
- __sadc__:  Acts as the system activity data collector, serving as a back-end
             for `sar`.
- __sa1__:   Collects and stores binary data in the system activity daily data
             file, functioning as a front-end to `sadc` and is intended for
             execution via cron or `systemd`.
- __sa2__:   Creates a summarized daily activity report, operating as a
             front-end to `sar` and designed for cron or `systemd` execution.
- __sadf__:  Presents data collected by `sar` in various formats (CSV, JSON,
             XML etc.), facilitating data exchange with other programs.
             This command also supports generating graphs for different
             activities recorded by `sar` in SVG format.

### System Statistics Collected by sar

- Input/Output and Transfer Rate Statistics: Includes global, per device, per
  partition, and per network filesystem data.
- CPU Statistics: Covers global and per-CPU metrics, with support for
  virtualization architectures.
- Memory Utilization: Encompasses statistics on memory, hugepages, and swap
  space.
- Virtual Memory and Paging: Details virtual memory metrics, paging, and fault
  statistics.
- Process Creation: Tracks process creation activity.
- Interrupt Statistics: Includes global, per CPU, and per interrupt data,
  covering APIC interrupt sources, hardware, and software interrupts.
- Network Statistics: Offers comprehensive data on network interface activity,
  failures from network devices, traffic statistics for IP, TCP, ICMP, UDP
  protocols (SNMPv2 standards), and IPv6-related protocols.
- Specialized Statistics: Encompasses fibre channel traffic, softnet
  (software-based network processing), NFS server and client activity, socket
  statistics, run queue and system load, kernel internal tables utilization,
  swapping, TTY device activity, and power management (CPU clock frequency, fan
  speed, device temperature, voltage inputs).
- Additional Metrics: Covers USB devices connected to the system, filesystems
  utilization (inodes and blocks), and Pressure-Stall Information statistics.

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2024-03-05 | Add link to mpstat                                   |
| 0.1.0   | 2024-03-01 | Initial release                                      |


