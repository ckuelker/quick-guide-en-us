---
title:  Make
author: Christian Külker
date: 2023-03-31
version: 0.1.1
locale: en_US
lang: en
type: title
disclaimer: False
toc: True
categories:
- Build
commands:
- make
tags:
- Make
description: Information around make

---

Compiling software, or building it, is a process that is done over and over
again, making build automation one of the earliest tasks in software
development. Introduced in April 1976 by Stuart Feldman at Bell Labs, Make
was first used internally.

Over time, Make became a widely used tool for software developers, making it
easier to manage complex software projects. However, the original
implementation of Make was limited in its ability to handle file dependencies
and limited in the abilitt to handle more complex build processes.

The Free Software Foundation developed 'GNU make', which expanded
on the capabilities of the original `make` program.

GNU make was released under an open source license, which allowed developers to
modify and redistribute the code freely. This is discussed in the history of
[the GNU project](https://www.gnu.org/gnu/thegnuproject.html).

The popularity of Make led to the creation of many alternative implementations,
including BSD Make (`pmake`, `bmake`, `fmake`), Microsoft `nmake`, Sun's
distributed `dmake`, and others. I also influenced [Apache
Ant](https://en.wikipedia.org/wiki/Apache_Ant) in 2000, Rake, MSBuid and many
others.

Today, `make` continues to be widely used in software development, particularly
in the Unix and Linux communities. Its popularity can be attributed to its
simplicity, flexibility, and open-source nature.

Most Make frameworks are based on two parts: a program, often called `make`,
and a control file, sometimes called `Makefile`, which together build
executable programs and libraries from source code by reading the file called
`Makefile`.  The `Makefile` is the receipt that tells make what and how to
compile and its dependencies.

- [GNU make](GNU-Make/make.md)

<!--
## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2023-03-31 | Improve writing, add more content                    |
| 0.1.0   | 2023-02-24 | Initial release                                      |
-->
