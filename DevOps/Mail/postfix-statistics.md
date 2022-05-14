---
title: Postfix Statistics
author: Christian Külker
date: 2022-05-09
version: 0.1.2
type: doc
disclaimer: True
TOC: True
categories:
- Mail
commands:
- pflogsumm
- mailx
- hostname
tags:
- Pflogsumm
- Poststats
- Mailx
description:
  As many communication servers, to know if the service performs at an expected
  level, a statistic overview comes in handy to evaluate service health.

---

## Overview

This document aggregates methods of creating mail server statistics. Starting
with version 0.1.0 it gives information about `pflogsumm.pl`.

A list of software that has mail statistic features.

| Name             | Cov. | URL                                             |
| ---------------- |----- | ----------------------------------------------- |
| pflogsum         | Yes  | https://jimsun.linxnet.com/postfix_contrib.html |
| lightmeter       | No   | https://gitlab.com/lightmeter/controlcenter/    |
| Postscreen-Stats | No   | https://github.com/jvehent/Postscreen-Stats     |
| PostConf         | No   | https://www.postconf.com/docs/spamrep/          |
| AWStats          | No   | https://awstats.sourceforge.io/                 |
| Mailgraph        | No   | https://mailgraph.schweikert.ch/                |
| MRTG w. scripts  | No   | http://taz.net.au/postfix/mrtg/                 |
| Logrep           | No   | https://sourceforge.net/projects/logrep/        |

A list of software that has no mail statistic features.

| Name             | Cov. | URL                                             |
| ---------------- |----- | ----------------------------------------------- |
| Poststats        | No   | https://github.com/jpylypiw/poststats           |


### History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2022-05-09 | Change level of sections (one down)                  |
| 0.1.1   | 2022-05-05 | Add poststats, list of software, links, front matter |
| 0.1.0   | 2022-05-04 | Initial release with `pflogsumm.pl`                  |

## Pflogsumm.pl

When searching for "postfix mail statistic" the first 10 results are referring
to a Perl script that analyses the Postfix log files. `pflogsumm.pl` is a
script written by James S. Seymour and copyrighted 1998-2004 under the `GPL
v2+` license and lives on
[https://jimsun.linxnet.com/postfix_contrib.html](https://jimsun.linxnet.com/postfix_contrib.html)
as of this writing (2022-05-04).

The `pflogsumm` scripts aims just an overview of past mail server activities to
give an administrator a 'head-up' in case of troubles.

### Installation

```shell
aptitude install pflogsumm
```

### Configuration

While some contributions, for example by [Falko
Timme](https://www.howtoforge.com/postfix-monitoring-with-mailgraph-and-pflogsumm-on-debian-lenny),
are suggesting to configure `logrotate` to have a dedicated log over 24 hours,
I got the impression that `pflogsumm` can do this already by itself via a time
stamp query and keywords like `yesterday`. I would agree, in case of using the
`yesterday` feature, it would depend at which time the cron job needs to be
executed and after 24h a re-invocation of the same job would give different
result. So a recreation of the reports would be more difficult. However usually
as a 'heads-up' using the `yesterday` scope as a one time shot cron job seems
justified.

### Invocations

As `pflogsumm` has a lot of command line options it might be advisable to
create a script to execute it.

For a daily (yesterday) summary:

~~~
#!/bin/bash
# To be executed after 24:00 every day (0:10)
/usr/sbin/pflogsumm -d yesterday --iso-date-time /var/log/mail.log 2>&1 |\
/usr/bin/mailx -s "`hostname --fqdn` daily mail statistic" postmaster
~~~

Then add a line to cron:

~~~
10 0 * * * /usr/local/sbin/postfix-statistics-pdflogsumm-yesterday
~~~

For a weekly summary, for Debian 10 and 11 `logrotate` is configured for some
`locale` to start the week on Sunday 00:00.  So you would need to execute the
script 10 before the end of the week on the current log file or 10 minutes
after the week started on the last log file. We use the latter:

~~~
#!/bin/bash
# To be executed after 24:00 on every Sunday (4:10)
/usr/sbin/pflogsumm --iso-date-time /var/log/mail.log.1 2>&1 |\
/usr/bin/mailx -s "`hostname --fqdn` weekly mail statistic" postmaster
~~~

Then add a line to cron

~~~
10 4 * * 0 /usr/local/sbin/postfix-statistics-pdflogsumm-week
~~~

## Poststats

Unlike the name the `postats` tool (GPLv3), despite the name is postfix monitor
with a web interface and mail sending capability triggered via cron. Since it
seems not providing a statistic it will not covered in this document.

### Links

- [https://github.com/jpylypiw/poststats](https://github.com/jpylypiw/poststats)

## Links

- [https://www.postfix.org/addon.html](https://www.postfix.org/addon.html)