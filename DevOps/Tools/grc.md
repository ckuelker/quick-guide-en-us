---
title: Grc
author: Christian Külker
date: 2020-12-17
version: 0.1.0
type: doc
disclaimer: True
TOC: True
categories:
- Tools
tags:
- Grc
description: Make logfiles colorful

---

The Python tool [grc] is very useful to catch problems in logfiles by
highlighting certain keywords and sections. It generic colourize text and can
be used to colourize logfiles and output of commands. It comes with a set of
pre configured expressions and it can be configured via additional regular
expressions. 

The current version as of Debian 10 Buster is `1.11.3-1` and it is developed on
[github] under the GPLv2.


## Installation

```shell
aptitude install grc
```

## Usage

Simplest:

```
grc netstat
grc ping hostname
grc tail /var/log/syslog
grc ps aux
```

Simple:

```shell
grc tail -f /var/log/apache2/error.log
```

Advanced:

Without [grc]:

```shell
tail -f myfile.log | perl -pe 's/SEVERE/\e[1;31m$&\e[0m/g'
```

And with [grc] for the first time:

```shell
mkdir ~/.grc
echo "regexp=SERVERE" > ~/.grc/myfile
echo "color=red" >>  ~/.grc/myfile
grc -c myfile tail -f myfile.log
```

And with [grc] for the second time and all times:

```shell
grc -c myfile tail -f myfile.log
```

The [github] `README.markdown` has more info on this.


## Links

[grc]: http://kassiopeia.juls.savba.sk/~garabik/software/grc.html
[github]: https://github.com/garabik/grc

