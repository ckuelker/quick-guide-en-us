---
title: Kubernetes
author: Christian Külker
date: 2021-05-21
version: 0.1.0
type: doc
disclaimer: True
toc: True
PDF: False
categories:
- Container
tags:
- Kubernetes
description: Kubernetes

---

## Introduction

The word Kubernetes ([κυβερνήτης] originates from Greek and means "helmsman" or
"pilot" or "governor", and is the etymological root of cybernetics)

![Dedicated - Virtual - Container](sys-virt-container0.1.0.png){width=100%}

**Dedicated** server are easy to maintain and every application can talk to
each other application directly.

**Virtualized** servers allows to run multiple servers (VM) on one hardware
with good security separation. Virtualisation allows a faster response and
allocation of different hardware towards applications that have a volatile
load.

**Containerized** servers share some advantages from virtualised servers, but
with less security in separation of instances. The advantage is that only one
OS needs to be managed, with the cost that only similar applications can be
run.  It would for example be difficult (or impossible) to run 32 bit big
endian applications together with 64 bit small endian and `arm64` applications.
Among many advantages containerized servers might suffer from less secure
applications as the libraries are application depended and are often not
administered by experienced system administrators but software engineers.
However this depends much on the culture of the organization that uses
containerized server.

Some advantages of containerized servers are:

- Metrics on application level
- Rollbacks
- Applications are somewhat OS independent
- Resource isolation
- Resource utilization

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2022-05-09 | +history; PDF: False (no Greek in Noto Sans CJK JP)  |
| 0.1.0   | 2022-05-09 | Initial release                                      |

[κυβερνήτης]: https://en.wikipedia.org/wiki/Kubernetes

