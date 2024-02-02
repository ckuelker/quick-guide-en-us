---
title: DevOps
type: title
author: Christian Külker
date: 2024-02-02
version: 0.1.0
cards: True
description: System Administration

---

> __DevOps__ is a set of practices that combines software development (Dev) and
> information-technology operations[^1] (Ops) which aims to _shorten_ the
> systems development life cycle and provide _continuous_ delivery with high
> software quality. [🡺 wikipedia](https://en.wikipedia.org/wiki/DevOps)

[^1]: The term _information-technology operations (Ops)_ is linked in Wikipedia
to [Data Center
Management](https://en.wikipedia.org/wiki/Data_center_management#Operations)
which is translated into only _Bahasa Indonesia_ and no other language (at the
time of writing 2020-02-19). Strange isn't it? In the German version of DevOps
in [🡺 Wikipedia](https://de.wikipedia.org/wiki/DevOps) the term _System
Administration_ is used.

* HPC: Not only development need to be rolled out fast also new versions
  of the OS needs to be deployed fast. As for HPC image solutions for the base
  OS and for basic software like the MPI stack are quite common. There are many
  solution: Bright Cluster Manager, Warewulf, OpenHPC just to name a few.
  Unlike traditional server deployment DevOps is available in HPC since decades
  as some part of HPC is to deploy and test out code. The flexibility is
  archived via queueing system (like Slurm and others) that sends new
  applications including its environment to the worker nodes.

<!--
## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2024-02-02 | Initial release                                      |
-->
