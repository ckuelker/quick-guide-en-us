---
title: Radicale
author: Christian Külker
date: 2023-04-01
version: 0.1.5
type: doc
disclaimer: True
toc: True
categories:
- Daemons
tags:
- Apache2
- Evolution
- Nginx
- Radicale
- Thunderbird
- Debian Package Build
description: A simple calendar, to-do list, memo and contact server.

---

[Radicale] is a simple [CalDAV] (calendar, to-do lists) and [CardDAV]
(contacts) server that is easy to install and maintain, does not require a
database, and can optionally use a git storage backend. Radicale does not
implement the full specification, but a subset.

Radicale may not be the best choice for mixed environments.

The Radicale software stack layers are as follows:

| Layer | Part     | Function | Provider  | Protocol, Format         |
| ----- | -------- | -------- | --------- | ------------------------ |
| 1     | Daemon   | Storage  | Radicale  | iCal/vCard               |
| 2     | Daemon   | Server   | Radicale  | CalDAV/CardDAV           |
| (3)   | Daemon   | Server   | nginx     | socket/proxy             |
| 4     | Transfer | Network  | internet  | http + TLS / https proxy |
| 5     | Client   | Receiver | Evolution | CalDAV/CardDAV           |
| 6     | Client   | GUI      | Evolution | Terminal, GTK, Web, ...  |

- Layer 3 is optional
- The provider of layer 3,4,5 and 6 can be changed
- Alternative to [nginx] is [apache2]

Radicale uses different formats at different levels to allow flexibility and
compatibility with different calendar clients.

At the storage layer, Radicale uses the iCalendar format (ICS) as the primary
format for storing calendar data. This is a standard format that is widely
supported by many different calendar clients. However, Radicale also supports
other formats such as vCard.

At the transport layer, Radicale uses various protocols to exchange calendar
data with different clients and servers. For example, it supports the CalDAV
protocol, which is a standard protocol for exchanging calendar data over the
Web.

## Radicale Installation Methods

The [Radicale] daemon can theoretically be built and installed via:

| #  | From | Via                 | Version  | Result | Notes                                                 |
| -- | ---- | ------------------- | -------- | ------ | ----------------------------------------------------- |
| 01 | tar  | [source]            | 3.0.6    | NT     |                                                       |
| 02 | tar  | [packages]          | 2.1.11-6 | OK     | See below                                             |
| 03 | git  | [pip]               | 3.0.6    |        | Package not tried, install works, function not tested |
| 04 | git  | debuild, dpkg       | 2.1.12   | NT     |                                                       |
| 05 | git  | debuild, dpkg       | 3.0.6*   | NG     | tried with changed Debian dir from 2.1.12-1           |
| 06 | git  | [stdeb]             | 3.0.6*   | NG     | tried latest                                          |
| 07 | git  | [stdeb]             | 3.0.6*   | NG     | tried with modified setup.py and setup/cfg            |
| 08 | tar  | [stdeb]             | 3.0.6    | NG     | tried                                                 |
| 09 | tar  | [stdeb]             | 3.0.6    | NG     | tried with modified setup.py and setup/cfg            |
| 10 | deb  | apt-get or aptitude | 2.1.11-6 | OK     | works                                                 |
| 11 | deb  | apt-get or aptitude | 2.1.12-2 | OK     | works                                                 |
| 12 | deb  | apt-get or aptitude | 3.0.6-1  | NA     |                                                       |
| 13 | deb  | apt-get or aptitude | 3.0.6-3  | NT     | TODO                                                  |

- Debian package
  - Via Debian official [package] repository (only version 2.1.12-1 as of
    2020-11-02 and 2.1.12-2 as of 2020-12-29 both in sid). The Debian wiki
    explains: https://wiki.debian.org/Radicale
  - Via a self built package

**Abbreviations:**

|    | Explanation       |
| -- | ----------------- |
| NT | not tried         |
| NG | not good (failed) |
| OK | good (passed)     |
| NA | not available     |

## Radicale Debian Packages

As of now (2020-12-29) only version `2.1.11-6` is available for Debian Buster
and `2.1.12-2` for Debian Sid. There is no official Debian package for `3.0.x`
in Debian Buster. The 3.0.x version comes with Debian Bullseye.

| Debian   | Version          |
|----------|------------------|
| Stretch  | 1.1.1+20160115-4 |
| Buster   | 2.1.11-6         |
| Bullseye | 3.0.6-3          |


### Build Dependencies

To the best of our knowledge, the following build dependencies should be met in
order to build [radicale] `2.1.11`

```bash
aptitude install help2man dh-buildinfo devscripts cdbs python3-atomicwrites \
python3-bcrypt python3-passlib python3-pytest python3-vobject quilt dh-python \
python3-setuptools
```

### Building A Debian Package

For [Radicale] `2.1.11`, the process is straightforward on Debian 10 Buster
after installing the dependencies.

```bash
wget http://deb.debian.org/debian/pool/main/r/radicale/radicale_2.1.11.orig.tar.gz
wget http://deb.debian.org/debian/pool/main/r/radicale/radicale_2.1.11-6.debian.tar.xz
tar xvzf radicale_2.1.11.orig.tar.gz
cd Radicale-2.1.11
tar xvJf ../radicale_2.1.11-6.debian.tar.xz
debuild -us -uc
```

This will create `radicale_2.1.11-6_all.deb`.

It is not possible (easy) to build Debian packages from the upstream git source
repository, as it does not contain a `debian` directory. The `debian` directory
for `2.1.11` does not work for `3.0.x`.

## Clients

- Newer versions of Thunderbird already include a calendar, no plugin needed
- Thunderbird with Lightning plugin (deprecated) requires CalDAV (not iCalendar
  (ICS)).
- [Evolution]

## Links

- [Apache2]
- [CalDAV]
- [CardDAV]
- [Evolution]
- [Nginx]
- [Radicale]

[Apache2]: https://httpd.apache.org/
[CalDAV]: https://en.wikipedia.org/wiki/CalDAV
[CardDAV]: https://en.wikipedia.org/wiki/CardDAV
[Evolution]: https://wiki.gnome.org/Apps/Evolution
[Nginx]: https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-open-source/
[package]: https://packages.debian.org/radicale
[pip]: https://radicale.org/3.0.html
[Radicale]: https://radicale.org/
[source]: https://github.com/Kozea/Radicale/tags
[stdeb]: https://pypi.org/project/stdeb/

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.5   | 2023-04-01 | Improve writing, add content                         |
| 0.1.4   | 2022-07-05 | Shell->Bash                                          |
| 0.1.3   | 2022-05-09 | Change level of section 'Links'                      |
| 0.1.2   | 2020-12-29 | Update front matter, links, Sid package version      |
| 0.1.1   | 2020-12-29 | Update build process                                 |
| 0.1.0   | 2020-11-02 | Initial release                                      |


