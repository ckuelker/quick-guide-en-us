---
title: Tail
author: Christian Külker
date: 2022-05-28
version: 0.1.1
type: doc
disclaimer: True
TOC: True
categories:
- Logfiles
commands:
- tail
- multitail
- grc
tags:
- Log
- Debug
- Logfiles
description: Tools viewing logfiles

---

## Introduction

Viewing logfiles in real time is one of the primary task for system
administrators. Usually it is the end of the log file were new information is
appended. Therefore the command `tail -f LOGFILE` is used. This document
describes `tail` as well as other tools.

## Tail

The Swiss knife for system administrators. The tool `tail` can be used for
various purposes, the most common ones are:

1. View some lines at the end of a huge file: `tail -n NUMBER FILE`
2. View a logfile updating in real time (follow): `tail -f FILE`
3. Use it in scripts like: `cat FILE|sort|uniq|tail -n 1`

In cases more that one log file need to be monitored, many approaches have
been implemented.

4. Quick and Dirty: `cd /var/log&&tail -f mail.{err,warn,info,log}`
5. Executing one `tail` per terminal
6. using `screen` and `tail`
7. using `tmux` and `tail`
8. ...

Or one can use `multitail`

## Multitail

The advantages of `multitail` over `tail` is that if you open 2 or more
logfiles with one command in one window, unlike `tail` `multitail` will group
the output.  Installation is easy.

```bash
aptitude install multitail
```

Unfortunately the command line options differ from `tail`, but some are
similar.

1. View some lines at the end of a huge file: `multitail -n NUMBER FILE`
2. View a logfile updating in real time (follow): `multitail -f FILE`
3. Use it in scripts like: `cat FILE|sort|uniq|mailtail -j -n 1` will **not**
   work
4. View many files: `cd /var/log&&multitail -f mail.{err,warn,info,log}`

Some differences to `tail` are:

- Leaving the `multitail` session with `q` not `CTL-C`
- Output is colorful
- Compared to `tail`, `multitail` is difficult to use in scripts
- Displays the filename per default

## Grc

If the color feature of `multitail` would be the only reason to switch from
`tail` to `multitail`, then one should consider using `grc` to colorize `tail`.

For mail logs `grc` creates in my opinion better color outputs, because it
highlights some constructs, like round and square brackets content and the
header of a `syslog` line can be easily distinguished from the rest.

```bash
aptitude install grc
```
- See [grc](grc.md) for advanced usage of `grc`
- Repository: <https://github.com/garabik/grc>

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2022-05-28 | Link to grc & grc repository, +History, mv ../Tools  |
| 0.1.0   | 2022-05-27 | Initial release                                      |


