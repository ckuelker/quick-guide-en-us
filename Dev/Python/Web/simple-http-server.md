---
title: Simple Http Server
linkTitle: Simple-Http-Server
author: Christian Külker
date: 2022-07-13
version: 0.1.1
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Python
- Python2
- Python3
commands:
- python
- python3
tags:
- Simple-Http-Server
- http.server
description: A simple HTML web server in python for projects

---

## Introduction

Sometimes it is handy to just have simple local web server that serves a web
directory in a non complex way. To be used in a web frame work development, in
a web site development, or just for validating the HTML part of the project
documentation.

## (Simple) Http Server

### Python 3

As with `python3` the module has changed from `python2`.

``` {#mycode .bash .numberLines startFrom="1"}
python3 -m http.server 8000
```
Point your web browser to `http://127.0.0.1:8000` or `http://localhost:8000`.

### Python 2

Go into the web directory you want to serve and execute.

``` {#mycode .bash .numberLines startFrom="1"}
python -m SimpleHTTPServer 8000
```

Point your web browser to `http://127.0.0.1:8000` or `http://localhost:8000`.

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2022-07-13 | Format for quick guide, update for python3           |
| 0.1.0   | 2016-07-19 | Initial release (as 0.1)                             |

