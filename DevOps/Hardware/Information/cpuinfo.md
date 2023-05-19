---
title: Cpuinfo
linkTitle: Cpuinfo
author: Christian Külker
date: 2023-05-19
version: 0.1.2
type: doc
disclaimer: True
toc: True
categories:
- SysAdm
tags:
- Cpuinfo
commands:
- grep
description: Scripting Cpuinfo

---

There is a lot of useful information in `/proc/cpuinfo`. However, modern server
boards have many CPUs and sometimes even more cores. In order to keep track of
this long information, sometimes this information needs to be pre-processed a
bit. This article is about some small scripts to get specific information.

## Model Name

```bash
grep -i 'model name' /proc/cpuinfo|uniq

Machine     Model Name
=========================================================================
X200 Tablet Intel(R) Core(TM)2 Duo CPU     L9400  @ 1.86GHz
Super Micro Intel(R) Xeon(R) CPU E5-2658 0 @ 2.10GHz
MIC         0b/01
```

## Number Of Calculation Cores

```bash
grep -i 'processor' /proc/cpuinfo|wc -l

Machine     Model Name                                              Value
=========================================================================
X200 Tablet Intel(R) Core(TM)2 Duo CPU     L9400  @ 1.86GHz             2
Super Micro Intel(R) Xeon(R) CPU E5-2658 0 @ 2.10GHz                   16
MIC         0b/01                                                     244
```

## Number Of Real Cpu Cores:

### Physical ID

```bash
grep -i "physical id" /proc/cpuinfo|sort -u|uniq|wc -l

Machine     Model Name                                              Value
=========================================================================
X200 Tablet Intel(R) Core(TM)2 Duo CPU     L9400  @ 1.86GHz             1
Super Micro Intel(R) Xeon(R) CPU E5-2658 0 @ 2.10GHz                    2
MIC         0b/01                                                       1
```

### Core ID

```bash
grep -i 'core id' /proc/cpuinfo|uniq|wc -l

Machine     Model Name                                              Value
=========================================================================
X200 Tablet Intel(R) Core(TM)2 Duo CPU     L9400  @ 1.86GHz             2
Super Micro Intel(R) Xeon(R) CPU E5-2658 0 @ 2.10GHz                   16
MIC         0b/01                                                      62
```

However, the `cpu cores` value for MIC is set to 61. And 244 cores divided by 4
is 61, not 62.

### CPU Cores

```bash
grep -i 'cpu cores' /proc/cpuinfo|uniq|sed -e 's%cpu cores.*: %%'

Machine     Model Name                                              Value
=========================================================================
X200 Tablet Intel(R) Core(TM)2 Duo CPU     L9400  @ 1.86GHz             2
Super Micro Intel(R) Xeon(R) CPU E5-2658 0 @ 2.10GHz                    8
MIC         0b/01                                                      61
```

### The Number Of Cores

To make the situation unambiguous, the number or _real_ cores should be
calculated as

```bash
echo $((`grep -i "physical id" /proc/cpuinfo|sort -u|uniq|wc -l` * \
`grep -i 'cpu cores' /proc/cpuinfo|uniq|sed -e 's%cpu cores.*: %%'`))

Machine     Model Name                                              Value
=========================================================================
X200 Tablet Intel(R) Core(TM)2 Duo CPU     L9400  @ 1.86GHz             2
Super Micro Intel(R) Xeon(R) CPU E5-2658 0 @ 2.10GHz                   16
MIC         0b/01                                                      61
```

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2023-05-19 | Improve writing                                      |
| 0.1.1   | 2022-07-02 | Commands meta data, shell->bash                      |
| 0.1.0   | 2016-06-22 | Initial release                                      |


