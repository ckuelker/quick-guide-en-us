---
title: Smem
linkTitle: Smem
author: Christian Külker
date: 2020-06-10
version: 0.1.0
type: doc
disclaimer: True
TOC: True
categories:
- Monitoring
commands:
- smem
tags:
- Smem
- Swap
- Memory
description:  Report memory usage with shared memory divided proportionally
...

## Show Swap Memory Usage For Processes

```shell
root@h:~# aptitude install smem
root@h:~# smem
  PID User     Command                         Swap      USS      PSS      RSS
  555 root     /sbin/agetty -o -p -- \u --        0      144      264     2072
  561 root     /sbin/agetty -o -p -- \u --        0      256      314     1808
  590 prg1     prg1/anvil                         0      216      334     2528
  525 root     /sbin/iscsid                       0      212      392     1780
  799 prg2     prg2/stats                         0      272      419     2620
  600 prg3     /usr/sbin/prg3 -x /etc/          888       28      431     2816
```

This is only a rough estimation. For precise measurement or other categories
see `man smem`.
