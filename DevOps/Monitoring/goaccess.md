---
title: Goaccess
author: Christian Külker
date: 2022-06-28
version: 0.1.1
type: doc
disclaimer: True
TOC: True
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
- written in C
- Only `ncurses` as a dependency
- Many web log formats (Apache, Nginx, Amazon S3, Elastic Load Balancing,
  CloudFront, Caddy, for the latest version. A bit less for Debian Buster)
- Beautiful terminal

## Installation

```bash
aptitude install goaccess
```

## Command Line Usage

The manual page gives a lot of command line options. A simple way to use it is
the command line mode that just uses the log file as a parameter.

Preferable this should be run inside `screen` or similar to maintain running,
even if the ssh connection drops. A restart is not a problem.


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

This will generate the file `report.hml`. View this with you browser or
create this in a directory served by the web server.

For Debian 10 Buster this will be created with missing missing Unicode
characters under Firefox. Other browsers display them.

## Real Time HTML Report Usage

This is just a short test and has some flaws that need to be addressed.

```
goaccess /var/log/nginx/access.log -o /web/server/root/report.html \
--log-format=COMBINED --real-time-html
```

The above command will generate a page under the web server root as well opens
a socked for the update. A short test under Debian 10 Buster reveals some
problems. The main problem is that the page is not displayed with Firefox or
Chromium. Chromium complained about insecure scripts. After waving, the page
was displayed. However as permanent solution this would not be acceptable.
Firefox received the page with code 200 but failed to load the `favicon.ico`
and three `fontawesome` web fonts.

## Links

- [home page]

[goaccess]: https://goaccess.io
[home page]: https://goaccess.io

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2022-06-28 | Grammar, formatting, minor improvements, shell->bash |
| 0.1.0   | 2021-05-22 | Initial release                                      |


