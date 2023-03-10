---
title: MemTest86 and Memtest86+
linkTitle: Memtest
author: Christian Külker
date: 2023-03-10
version: 0.1.1
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Memory
tags:
- Memtest86
- Memtest86+
description: MemTest86 and Memtest86+

---

## Introduction

There are many ways to test a computer's memory. Naively, memory can be thought
of as a binary object. While in binary systems, such as today's computers, the
state of a given memory cell should be binary; it is not true that the overall
state of the device (the memory module) is OK or Not OK (NG), so it can __not__
be considered binary. There are many states between OK and Not OK.  While in
many circumstances memory appears OK and is considered to be working, it can be
seen, not only in supercomputing, that memory as an instance of physics is not
binary and there are several levels of OK, depending on usage and user
acceptance. For some unknown reason, ECC memory is not considered a mainstream
object of use.  Therefore, some errors are not detected and therefore accepted
by the user, even though the user may not admit it. In supercomputers, memory
that throws errors is less welcome. Usually ECC is used where possible, but
even then the memory must be validated. Unfortunately, testing and evaluating
memory is a difficult and time-consuming business. The reason is that certain
value streams written to memory can cause an error while others do not. For
example, `11->00->11` might be an error-free pattern, while `11->01->10->11`
might not. This is because in certain memory modules, for example, cross-talk
between memory lanes is possible, or some other kind of local phenomena.

In an ideal world, a memory test must take into account the physical structure
of the memory being tested. While some supercomputers with custom-designed
memory spend years evaluating and qualifying memory tests, consumer electronics
such as the `i386` and successor architectures are typically not tested down to
the design level. A more general brute force approach is to write many
different patterns to memory and read them back. Doing this structurally and/or
randomly over a long period of time will not guarantee error-free memory, but
the probability of undetected errors decreases as the error-free time of a test
increases.

## Memtest86+ and MemTest86

_Memtest86+_ and _MemTest86_ are two well known memory tests for the x86
architecture.

| Test       | URL                        | Arch        | Active    | License     |
| ---------- | -------------------------- | ----------- | --------- | ----------- |
| Memtest86+ | https://www.memtest.org/   | x86, x86-64 | 1994-2022 | GPL v2.0    |
| MemTest86  | https://www.memtest86.com/ | x86, x86-64 | 2013-2022 | Proprietary |

## Memtest86+

### History

| Date      | Name             | Author                        |
| --------- | ---------------- | ----------------------------- |
| 2022      | Memtest86+ V6    | M Whitaker, S. Demeulemeester |
| 2020-2022 | PCMemTest        | Martin Whitaker               |
| 2004-2020 | Memtest86+ V1-V5 | Sam Demeulemeester            |
| 2004      | Memtest86 V3     | Eric Biedermann               |
| 1994-2002 | MemTest-86       | Chris Brady                   |

### Links

- [Wikipedia English](https://en.wikipedia.org/wiki/Memtest86)
- [Wikipedia German](https://de.wikipedia.org/wiki/Memtest86%2B)
- [Repository](https://github.com/memtest86plus/memtest86plus)

## MemTest86

[Wikpedia](https://en.wikipedia.org/wiki/Memtest86) claims, the original
_Memtest86_ was sold to _PassMark_ in February 2013. Therefore this section is
kept short.

### License

The [license](https://www.memtest86.com/tech_license-information.html) of
_MemTest86_ appears to be proprietary. A written form of the license was not
found on the web site (2022-10-25). When it comes to the license, it was
sometimes referred to for older versions as `Free` and/or `GPL`, while the
license of the newer version labeled as `Pro` or `Site` was not provided, nor
its usage restrictions.

### History

| Date       | Version            | License           |
| ---------- | ------------------ | ----------------- |
|            | MemTest86 V10 Site |                   |
|            | MemTest86 V10 Pro  |                   |
|            | MemTest86 V10 Free |                   |
| 2015-02-13 | MemTest86 V6       |                   |
| 2013-12-03 | MemTest86 V5       |                   |
| 2013-02    | MemTest86 v4       | (until 4.3.7 GPL) |

### Links

- [Wikipedia English](https://en.wikipedia.org/wiki/Memtest86)
- [Wikipedia German](https://de.wikipedia.org/wiki/Memtest86)

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2023-03-10 | Improve writing, change tables, typos                |
| 0.1.0   | 2022-10-25 | Initial release                                      |

