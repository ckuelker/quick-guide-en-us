---
title: Software RAID
linkTitle: Sw-RAID
author: Christian Külker
date: 2024-02-15
version: 0.1.1
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Hardware
- Storage
- HDD
- SDD
- NVMe
commands:
- mdadm
tags:
- RAID
- Software-RAID
description: Software RAID with mdadm

---

## Introduction

There are many FOSS frameworks for managing disks and accessing remote file
systems via NFS and Samba. These include FreeNAS and Open Media Vault (OVM).
However, this document describes the low-level view when using Software
Redundant Array of Independent/Inexpensive Disks (RAID) via `mdadm`.

## Software RAID with mdadm

RAID is not a backup. Data on the disks used in this document will
be __erased__.

### Installation

```bash
aptitude install mdadm
```

### RAID 0

Obviously you need at least two disks for RAID 0.

#### Setup

Use `fdisk` or your tool of choice to partition the HDD.

As root

```bash
mdadm --create --verbose /dev/md0 --level=0 --raid-devices=2 /dev/sda1 /dev/sdb1

mkdir /mnt/raid0
mkfs.xfs /dev/md0  # Or your filesystem of choice
mount /dev/md0 /mnt/raid0
```

#### Check Health

```bash
mdadm --detail /dev/md0
```

#### Mount at Startup

Edit `/etc/fstab`

~~~
/dev/md0 /mnt/raid0 xfs defaults,noatime 0 1
~~~

Add configuration to `mdadm`

```bash
mdadm --detail --scan | tee -a /etc/mdadm/mdadm.conf
```

Check if the configuration is OK.

```bash
reboot
```

Verify that the RAID comes up.


### RAID 1

Obviously you need also at least two disks for RAID 1.
The process is basically the same as for RAID 0.
Here the summary:

Use `fdisk` or your tool of choice to partition the HDD.

As root

```bash
mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sda1 /dev/sdb1

mkdir /mnt/raid1
mkfs.xfs /dev/md0  # Or your filesystem of choice
mount /dev/md0 /mnt/raid1
```

#### Check Health

```bash
mdadm --deatil /dev/md0
```

#### Mount at Startup

Edit `/etc/fstab`

~~~
/dev/md0 /mnt/raid1 xfs defaults,noatime 0 1
~~~

Add configuration to `mdadm`

```bash
mdadm --detail --scan | tee -a /etc/mdadm/mdadm.conf
```

Check if the configuration is OK.

```bash
reboot
```

Verify that the RAID comes up.


## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2024-02-15 | + RAID 1                                             |
| 0.1.0   | 2023-04-23 | Initial release                                      |

