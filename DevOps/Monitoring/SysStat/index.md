---
title: SysStat
author: Christian Külker
version: 0.1.2
date: 2024-03-08
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

- __sar__:   _"System Activity Reporter"_ gathers, reports, and stores system
             activity information
- __sadc__:  _"System Activity Data Collector"_ acts as the system activity
             data collector, serving as a back-end for `sar`.
- __sa1__:   (shell procedure variant of _sadc_) Collects and stores binary
             data in the system activity daily data file, functioning as a
             front-end to `sadc` and is intended for execution via cron or
             `systemd`.
- __sa2__:   (shell procedure variant of _sar_) Creates a summarized daily
             activity report, operating as a front-end to `sar` and designed
             for cron or `systemd` execution.
- __sadf__:  _"System Activity Data Formatter"_ presents data collected by
             `sar` in various formats (CSV, JSON, XML etc.), facilitating data
             exchange with other programs. This command also supports
             generating graphs for different activities recorded by `sar` in
             SVG format.

_sa1_ and _sa2_ are shell wrappers around _sadc_ and _sar_. The difference is
that the shell scripts handle corner cases and logging better and are therefore
suggested to be used for crond.

### System Statistics Collected by sar

- Input/Output and Transfer Rate Statistics: Includes global, per device, per
  partition, and per network files-ystem data.
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
- Additional Metrics: Covers USB devices connected to the system, file-systems
  utilization (inodes and blocks), and Pressure-Stall Information statistics.

## Debian

The Debian sysstat package integrates an entry within the system's crontab. For
comprehensive details, consult the file located at
`/usr/share/doc/sysstat/README.Debian`.

For the display of all-day statistics with `sar` to work, data needs to be
collected, but this is __disabled__ per default under Debian. The Debian
packages encompasses a system crontab file executing the `sa1` script.
However, it is initially in a __disabled__ state, resulting in the non-collection
of statistics. Consequently, executing `sar` without parameters typically
results in an error, indicated as

> Cannot open `/var/log/sysstat/saXX`: No such file or directory.

To rectify this issue and initiate data collection, activate the crontab
function by executing:

```bash
        dpkg-reconfigure sysstat
```

and affirmatively responding to the inquiry regarding the activation of
sysstat's cron job.

~~~
If this option is enabled the sysstat package will monitor system activities
and store the data in log files within /var/log/sysstat/.

This data allows the sar(1) command to display system statistics for the whole
day.

If you don't enable this option, the sar(1) command will only show the current
statistics.

Activate sysstat's cron job?

            <Yes>                                            <No>
~~~

Furthermore, the package installs specific system crontab files, namely
`/etc/cron.d/sysstat` and `/etc/cron.daily/sysstat` to collect data. Following
the example section of the man pages to install cron jobs is not necessary.
Undertaking such manual entries might result in concurrent executions of the
`sa2` script, potentially leading to operational errors.

### Ansible and Command-line

To automate the installation and configuration of sysstat debconf can be
used with Ansible to activate the cron job. However a non interactive
configuration fails, because of the file `/etc/default/sysstat`. So
the debconf configuration has to be done in one of the 3 ways:

1. Before package installation non interactively
2. After package installation interactively
3. After package installation and removal of `/etc/default/sysstat`
   and non interactively

Before we write the Ansible debconf question, we would need to understand what
question exactly is used in `sysstat`

```bash
debconf-show sysstat
* sysstat/enable: false
  sysstat/remove_files: true
```

On the command-line the following works __after__ package installation.

```bash
debconf-show sysstat
* sysstat/enable: false
  sysstat/remove_files: true
rm /etc/default/sysstat # Or edit it and set to "true"
echo sysstat sysstat/enable boolean true|debconf-set-selections
dpkg-reconfigure --frontend=noninteractive sysstat
debconf-show sysstat
* sysstat/enable: true
  sysstat/remove_files: true
```

With Ansible two more steps are needed.

1. A value in `/etc/default/sysstat` need to be changed
2. The sysstat server should be restarted.

```yaml
# adm-nox.yaml
---
- hosts: role_client
  gather_facts: no
  become: yes
  vars:
      ns: adm_nox
      packages:
          # SysStat (iostat, ...) System performance tools for Linux
        - sysstat
  tasks:
    - name: "{{ ns }}: Install and update packages"
      apt:
        name: "{{ packages }}"
        state: latest
    - name: "{{ ns }}: Enable sysstat"
      lineinfile:
        path: /etc/default/sysstat
        regexp: '^ENABLED=".*"'
        line: 'ENABLED="true"'
        create: yes
    - name: "{{ ns }}: Pre-configure sysstat package"
      ansible.builtin.debconf:
        name: sysstat
        question: sysstat/enable
        vtype: "boolean"
        value: "true"
      become: yes
      register: dpkg_reconfigure
    - name: "{{ ns }}: Reconfigure sysstat package"
      ansible.builtin.shell:
        cmd: dpkg-reconfigure --frontend=noninteractive sysstat
      when: dpkg_reconfigure.changed
      become: yes
      notify: restart sysstat
  handlers:
    - name: restart sysstat
      service:
        name: sysstat
        state: restarted
```

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2024-03-08 | Improve introduction, add Debian section,  Ansible   |
| 0.1.1   | 2024-03-05 | Add link to mpstat                                   |
| 0.1.0   | 2024-03-01 | Initial release                                      |
