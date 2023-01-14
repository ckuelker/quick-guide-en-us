---
title: Mail Policy Overview
linkTitle: Overview
author: Christian Külker
date: 2023-01-13
version: 0.1.0
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Mail
- Mail Policy
tags:
- Mail Policy Overview
- Postfix
- Policy Weight Daemon
commands:
- policyd-weight
packages:
- policyd-weight
description: Overview of mail server policy daemons with the focus on Postfix

---

This is overview is not complete. It started 2023-01-13 as a aggregation
document for Postfix policy daemons to understand the current state of policy
daemons, because some of the well known policy daemons, mentioned in
literature, are no longer maintained.

[SMTPD_POLICY_README]: https://www.postfix.org/SMTPD_POLICY_README.html
[POSTSCREEN_README]: https://www.postfix.org/POSTSCREEN_README.html
[Greylisting]: https://en.wikipedia.org/wiki/Greylisting_(email)

The Postfix mail server can block or accept mails by various methods
internally. In cases where this capability is not sufficient a Postfix `smtpd`
policy (see [SMTPD_POLICY_README][]) can be used to add functionality to
Postfix. Examples for this is Postfix [greylisting][].

## Open Source Policy Daemons

| Name              | License | Deb | Lang | Updated |
| ----------------- | ------- | --- | ---- | ------- |
| [Policy Weight][] | GPL2    | yes | Perl | 2009    |

## Policy Weight Daemon

[Policy Weight]: https://en.wikipedia.org/wiki/Policyd-weight
[Policy Weight home]: https://www.policyd-weight.org/
[Policy Weight Debian package]: https://tracker.debian.org/pkg/policyd-weight
[Policy Weight source code]: https://unknown.org/
[Policy Weight unofficially source code]: https://github.com/Whissi/policyd-weight
[Policy Weight Debian popularity]: https://tracker.debian.org/pkg/policyd-weight
[Policy Weight to do list]: https://github.com/Whissi/policyd-weight/blob/master/todo.txt
[Policy Weight new mailing list]: https://listen.jpberlin.de/mailman/listinfo/policyd-weight-users
[Policy Weight new mailing list archive]: https://listen.jpberlin.de/pipermail/policyd-weight-users/
[Policy Weight old mailing list archive]: https://www.mail-archive.com/policyd-weight-list%40ek-muc.de/
[Policy Weight Wikipedia]: https://en.wikipedia.org/wiki/Policyd-weight

The Policy Weight Daemon is a well known policy daemon for Postfix. Also the
German "Das Postfix Buch" mentioned it and the [Policy Weight Debian
popularity][] was high between 2010 and 2016. From the [Policy Weight
unofficially source code][] repository it can be understood that the
development was suspended probably around 2013 (2018-5) or earlier and the last
stable version and to-do-list is from 2009. The [Policy Weight source code][]
location is unknown as of 2023-01-13. The [Policy Weight home][] declares
itself as "Project discontinued".

As of the [Policy Weight Debian package][] page, there are some Debian package
management task (bugs) known, but no major software bugs as of 2023-01-13.
However without an upstream buck tracker, this is difficult to know. From the
[Policy Weight to do list][] `todo.txt` of the [Policy Weight unofficially
source code][] is is clear that some major `IPv6` features are missing.

The [Policy Weight old mailing list archive][] can be seen up to 2010. The
[Policy Weight new mailing list][] location contains the [Policy Weight new
mailing list archive][] up to 2016.

### Function

The Policy Weight Daemon is used by Postfix `smtpd` before mail body `DATA` is
accepted. Various checks are performed by the daemon using caching to minimize
queries. Individual checks are weighted (hence the name) with a score (that
can be changed by the administrator) which is aggregated. If the sum is over a
definable threshold, the mail gets rejected before `DATA` is fetched, saving
bandwidth.

### Features

- `DNSBL`
- `RHSBL`
- `MX` (checks of `DNS` `MX` records)
- `A` `DNS` (checks of `DNS` `A` records)
- `HELO` _smtp_

### Discussion

The main benefits of policy daemons are: rejecting mail before delivering and
saving bandwidth. This is in contrast to SpamAssassin, Amavis and others that
operates after mail `DATA` is fetched. Policy Weight do not add delays, as
_greylisting_ would do. It performs rejection of fake `HELO` or sender
information with better accuracy and less false positives than the Sender
Policy Framework (SPF).

Parts of the functionality might be covered with newer versions of Postfix,
version 2.8 and up. The [POSTSCREEN_README][] for example gives information
about testing `MX` (`MX` policy test) and other DNS information. While Policy
Weight acts __after__ `smtpd`,
[`postscreen`](https://www.postfix.org/postscreen.8.html) works __before__
`smtpd`, this could save even more bandwidth.

### Further Read

- <https://doku.fietz.net/index.php/Policyd-weight>
- <https://www.heinlein-support.de/upload/mk4/2-08_policyd-weight.pdf>
- <https://dokuwiki.nausch.org/doku.php/centos:mail_c6:spam_2>

### Links

- [Policy Weight home][]
- [Policy Weight Debian package][]
- [Policy Weight source code][] is unknown
- [Policy Weight unofficially source code][]
- [Policy Weight Debian popularity][]
- [Policy Weight to do list][]
- [Policy Weight new mailing list][]
- [Policy Weight new mailing list archive][]
- [Policy Weight old mailing list archive][]
- [Policy Weight Wikipedia][]

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2023-01-13 | Initial release Policy Weight                        |

