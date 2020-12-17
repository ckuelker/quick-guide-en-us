---
title: Bmon
author: Christian Külker
date: 2020-06-12
version: 0.1.0
type: doc
disclaimer: True
TOC: True
categories:
- Monitoring
tags:
- Bmon
description: Tool to capture networking related statistics visually

---

The monitoring and debugging tool [bmon] can be used to capture network relates
statistics inside a terminal and present this in an intuitive way. It features
different output methods including an interactive curses UI and a programmable
text output, that can be used for scripting.

## Installation

```shell
# aptitude install bmon
```

Will install `bmon`, `libconfuse-common` and `libconfuse1`.

## Usage

![bmon](bmon-0.1.0.png)

```shell
$ bmon
```

## Links

- [bmon] redirects to [github]

[bmon]: http://www.infradead.org/~tgr/bmon/
[github]: https://github.com/tgraf/bmon/
