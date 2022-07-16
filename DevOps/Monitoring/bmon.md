---
title: Bmon
author: Christian Külker
date: 2022-06-03
version: 0.1.1
type: doc
disclaimer: True
toc: True
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

```bash
aptitude install bmon
```

Will install `bmon`, `libconfuse-common` and `libconfuse1`.

## Usage

![bmon](bmon-0.1.0.png)

```bash
bmon
```

## Links

- [bmon] redirects to [github]

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2022-06-03 | Change shell->bash, +history                         |
| 0.1.0   | 2020-06-12 | Initial release                                      |

[bmon]: http://www.infradead.org/~tgr/bmon/
[github]: https://github.com/tgraf/bmon/
