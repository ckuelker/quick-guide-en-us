---
title: Html2markdown
author: Christian Külker
date: 2023-03-29
version: 0.1.0
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Tools
commands:
- python3-html2text
tags:
- Html2markdown
- Markdown
- HTML
description: Html2markdown is also very useful to save a web page as markdown.

---

The `html2markdown.py3` tool from the `python3-html2text` package can be used
to fetch HTML pages and save them as Markdown. This can be useful for saving
terms of use or privacy policy pages and making them searchable locally.

## Installation

```bash
aptitude install python3-html2text
```

## Usage

```bash
python3 /usr/bin/html2markdown.py3 URL > file.md
```

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2023-03-29 | Improve writing and initial release                  |

