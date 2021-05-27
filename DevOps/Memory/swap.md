---
title: Swap
author: Christian Külker
date: 2021-05-27
version: 0.1.0
type: doc
disclaimer: True
TOC: True
categories:
- Memory
commands:
- fallocate
- free
- mkswap
- smem
- swapoff
- swapon
- top
- vmstat
tags:
- Swap
description: Simple commands around old fashioned swap space

---

A temporary memory (aka swap) is not strictly needed by a Linux system. However
some Linux distributions refuse to install if not provided. In some cases
changing or adding swap space on the fly (if a system has no swap space) is a
seldom task to do and sometimes the correct commands need to be remembered or
looked up (in this document).

Linux provides two types of swap space. By default, most Linux
installations use or create a swap partition, but it is also possible to use a
specially configured file as a swap file.

## Swap Games With Files

```shell
# See how full the disk is
df -h
# Prints the summary (if any) of swap spaces
swapon -s
# See how many main memory and swap is available
free
free -m
cat /proc/swaps
top
# See the swap in and out
vmstat
# See the swap usage of singe applications
smem -s swap
# In case there is no swap and we want to create 2G of swap space
fallocate -l 2G /swapfile
# See the result
ls -lh /swapfile
# Swap space should not be readable by world
chmod 600 /swapfile
# Create a swap signature
mkswap /swapfile
# Use the swap file
swapon /swapfile
# Show a summary
swapon -s
# This should be visible in the memory oververview
free -m
# Turn off the swap partition with the command which turns off all swap spaces
swapoff -a
# For some non Debian systems: To mount the swap file on boot, add an entry
# to /etc/fstab
echo "/swapfile none swap sw 0 0" >>/etc/fstab
# For Debian systems: To mount the swap file on boot, add an entry to
# /etc/fstab
echo "/swapfile swap swap defaults 0 0" >>/etc/fstab
```


