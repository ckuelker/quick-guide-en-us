---
title: Tail
author: Christian Külker
date: 2023-04-06
version: 0.1.2
type: doc
disclaimer: True
toc: True
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

Viewing log files in real time is one of the most important tasks for system
administrators. Typically, it is the end of the log file where new information
is appended. Therefore the command `tail -f LOGFILE` is used. This document
describes `tail` as well as other tools.

## Tail

The Swiss Army knife for system administrators. The `tail` tool can be used for
various purposes, the most common ones are

1. View some lines at the end of a huge file: `tail -n NUMBER FILE`
2. View a logfile that is updated in real time (follow): `tail -f FILE`.
3. Use it in scripts like: `cat FILE|sort|uniq|tail -n 1`

In cases where more than one log file needs to be monitored, many approaches
have been implemented.

4. Quick and Dirty: `cd /var/log&&tail -f mail.{err,warn,info,log}`
5. Executing one `tail` per terminal
6. Using `screen` and `tail`
7. Using `tmux` and `tail`
8. ...

Or one can use `multitail`

## Multitail

The advantage of `multitail` over `tail` is that if you open 2 or more log
files with one command in one window, `multitail` will group the output, unlike
`tail`.  Installation is simple.

```bash
aptitude install multitail
```

Unfortunately, the command line options are different from `tail`, but some are
similar.

1. View some lines at the end of a huge file: `multitail -n NUMBER FILE`
2. View a log file updated in real time (follow): `multitail -f FILE`.
3. Use it in scripts like `cat FILE|sort|uniq|maultitail -j -n 1` will __not__
   work.
4. View many files: `cd /var/log&&multitail -f mail.{err,warn,info,log}`

Some differences to `tail` are:

- Leaving the `multitail` session with `q` not `CTL-C`
- Output is colored
- Compared to `tail`, `multitail` is difficult to use in scripts
- Displays the file name by default

## Grc

If the color feature of `multitail` would be the only reason to switch from
`tail` to `multitail`, you should consider using `grc` to colorize `tail`.

For mail logs, `grc` produces better color output, in my opinion, because it
highlights some constructs, like the contents of round and square brackets, and
the header of a `syslog` line is easily distinguishable from the rest.

```bash
aptitude install grc
```
- See [grc](grc.md) for advanced usage of `grc`
- Repository: <https://github.com/garabik/grc>

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2023-04-06 | Improve writing, typos                               |
| 0.1.1   | 2022-05-28 | Link to grc & grc repository, +History, mv ../Tools  |
| 0.1.0   | 2022-05-27 | Initial release                                      |


