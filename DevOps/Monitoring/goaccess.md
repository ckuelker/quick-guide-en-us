---
title: Goaccess
author: Christian Külker
date: 2023-03-14
version: 0.1.2
type: doc
disclaimer: True
toc: True
categories:
- Monitoring
commands:
- goaccess
tags:
- Goaccess
description: Goaccess can monitor Apache, Nginx and other web server log files

---

[Goaccess] can monitor a web server's log file in real time. Either by
providing output to the console or by creating a static or real-time HTML
report.

## Features

- MIT license
- Fast, real-time, millisecond/second updates
- Written in C
- Only `ncurses` as a dependency
- Many weblog formats (Apache, Nginx, Amazon S3, Elastic Load Balancing,
  CloudFront, Caddy, for the latest version. Somewhat less for Debian Buster)
- Beautiful terminal

## Installation

```bash
aptitude install goaccess
```

## Command Line Usage

The manual page gives a lot of command line options. An easy way to use it is
the command line mode, which only uses the log file as a parameter. Preferably
this should be run within `screen` or similar to keep it running even if the
ssh connection is lost. Restarting is not a problem. Configuration can be done
(a) via configuration file and command line option _or_ (b) via interactive
setup.

__(a)__

```bash
echo 'time-format %T' >> /etc/goaccess.conf
echo 'date-format %d/%b/%Y' >> /etc/goaccess.conf
echo 'log-format %h %^[%d:%t %^] "%r" %s %b "%R" "%u"' >> /etc/goaccess.conf
goaccess --log-format=COMBINED access.log
```

__(b)__


```bash
cd /var/log/nginx
goaccess access.log
+-----------------------------------------------------------+
| Log Format Configuration                                  |
| [SPACE] to toggle - [ENTER] to proceed - [q]uit           |
|                                                           |
| [x] NCSA Combined Log Format                              |
| [ ] NCSA Combined Log Format with Virtual Host            |
| [ ] Common Log Format (CLF)                               |
| [ ] Common Log Format (CLF) with Virtual Host             |
| [ ] W3C                                                   |
| [ ] Squid Native Format                                   |
|                                                           |
| Log Format - [c] to add/edit format                       |
|                                                           |
|                                                           |
| Date Format - [d] to add/edit format                      |
|                                                           |
|                                                           |
| Time Format - [t] to add/edit format                      |
|                                                           |
+-----------------------------------------------------------+
```

This will output something like this:

~~~
 Dashboard - Overall Analyzed Requests (22/May/2021 - 2[Active Panel: Visitors]

  Total Requests  1276 Unique Visitors  283 Unique Files 293 Referrers 0
  Valid Requests  1274 Init. Proc. Time 1s  Static Files 20  Log Size  444.80 Ki
B Failed Requests 2    Excl. IP Hits    0   Unique 404   244 Bandwidth 3.35 MiB
  Log Source      access.log

 > 1 - Unique visitors per day - Including spiders                  Total: 1/1

 Hits      h% Vis.      v%   Bandwidth Data
 ---- ------- ---- ------- ----------- ----
 1274 100.00%  283 100.00%    3.35 MiB 22/May/2021 |||||||||||||||||||||||||||||
~~~

It is possible to scroll down to see other sections.

~~~
   2 - Requested Files (URLs)                                   Total: 293/293

 Hits     h% Vis.     v%   Bandwidth Mtd Proto    Data
 ---- ------ ---- ------ ----------- --- -------- ----
 63   11.39%   43  8.63%  139.10 KiB GET HTTP/1.1 /
 58   10.49%   57 11.45%  286.99 KiB GET HTTP/1.1 /testarchiv.example.com/
 41    7.41%   31  6.22%  198.07 KiB GET HTTP/1.1 /testarchiv.example.com/News
 37    6.69%   37  7.43%  458.63 KiB GET HTTP/1.0 /testarchiv.example.com/
 12    2.17%   12  2.41%  660.88 KiB GET HTTP/1.0 /testarchiv.example.com/Test
 8     1.45%    7  1.41%   34.16 KiB GET HTTP/1.1 /testarchiv.example.com/Moin
 7     1.27%    7  1.41%   97.73 KiB GET HTTP/1.0 /testarchiv.example.com/News

   3 - Static Requests                                            Total: 20/20

 Hits     h% Vis.     v%   Bandwidth Mtd Proto    Data
 ---- ------ ---- ------ ----------- --- -------- ----
 5    20.00%    3 13.04%   925.0   B GET HTTP/1.1 /robots.txt
 2     8.00%    2  8.70%   13.22 KiB GET HTTP/1.1 /Xargs-Request-2.1.3.8.tar.gz
 1     4.00%    1  4.35%   10.55 KiB GET HTTP/1.1 /Module-Build-Xargs-5.2.9.tar
 1     4.00%    1  4.35%   185.0   B GET HTTP/1.1 /markdown-style.css
 1     4.00%    1  4.35%   10.69 KiB GET HTTP/1.1 /Module-Build-Xargs-7.3.2.tar
 1     4.00%    1  4.35%    2.07 KiB GET HTTP/1.1 /abc/style.css
 1     4.00%    1  4.35%    6.76 KiB GET HTTP/1.1 /Xargs-Soft-Simple-2.1.3.8.tar
 ~~~

## Static HTML Report Usage

Crate a static HTML report in a dedicated directory `TEST`:

```
cd
mkdir TEST
cd TEST
goaccess /var/log/nginx/access.log -o report.html --log-format=COMBINED
```

This will create the `report.hml` file. View it with your browser or create it
in a directory served by the web server. For Debian 10 Buster this will be
created with missing Unicode characters in Firefox. Other browsers will display
them.

## Real Time HTML Report Usage

This section, using the Real-Time HTML Reporting feature, was just a quick test
and showed some bugs that would need to be fixed.

```
goaccess /var/log/nginx/access.log -o /web/server/root/report.html \
--log-format=COMBINED --real-time-html
```

The above command will generate a page under the web server root as well as
open a socked for the update. A quick test on Debian 10 Buster shows some
problems. The main problem is that the page does not display with Firefox or
Chromium. Chromium complains about insecure scripts. After waving the page was
displayed. However, this would not be acceptable as a permanent solution.
Firefox received the page with code 200, but failed to load the `favicon.ico`
and three `fontawesome` web fonts.

## Links

- [home page]

[goaccess]: https://goaccess.io
[home page]: https://goaccess.io

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2023-03-14 | Improve writing, add command line example            |
| 0.1.1   | 2022-06-28 | Grammar, formatting, minor improvements, shell->bash |
| 0.1.0   | 2021-05-22 | Initial release                                      |

