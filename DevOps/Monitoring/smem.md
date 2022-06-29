---
title: Smem
linkTitle: Smem
author: Christian Külker
date: 2022-06-29
version: 0.1.1
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

---

## Installation

As root

```bash
aptitude install smem
```

## Show Swap Memory Usage For Processes

```bash
smem
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

## Show Sorted Swap Memory Usage

One strong incentive to use `smem` is its sort option to understand the
processes that uses most of the swap space. As root do:

```bash
smem -s swap
...
25488 user6    fishtai/segg                      0     1272     1613     4172
    1 root     /sbin/init                        4     1528     2687     8740
18521 user4    (sd-pam)                          4     1624     2226     4716
18597 root     (sd-pam)                          4     1560     2175     4632
  438 root     /usr/lib/udisks2/udisksd         24     3832     4562     9376
  461 shelldb  /usr/sbin/shell --foregroun      36  1280540  1281410  1285724
  628 root     /usr/sbin/cron -f                40      260      309     2248
  653 fishtai  fishtai/anringa                  76      144      213     1792
  656 root     fishtai/config                  104     1280     1361     3268
  604 root     /usr/sbin/fishtai -F            152      248      328     2280
  654 root     fishtai/log                     184      180      233     1936
  605 root     /usr/bin/python3 /usr/bin/f     200    16276    17720    23088
  625 root     /usr/sbin/sshd -D               220      644      982     5424
  910 root     /usr/lib/mydbarg/sbin/main      492      396      647     2952
  440 root     /usr/bin/python3 /usr/sbin/     644    20728    22874    29808
  678 process  /usr/sbin/process  -x /etc/     940        4      361     2336
  679 process  /usr/sbin/process  -x /etc/    1236     7732     8245    12176
  610 daemon   /usr/sbin/goto5 -u daem -4     6580    10972    11145    14288
  964 amigod   /usr/sbin/amigodd-old (ch2-   29764    38600    73196   111100
  933 amigod   /usr/sbin/amigodd-old (main   46120    19580    54113    92244
 ```

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2022-06-29 | Swap sort + install section, history, shell->bash    |
| 0.1.0   | 2020-06-10 | Initial release                                      |



