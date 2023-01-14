---
title: Mail Policy Overview
linkTitle: Overview
author: Christian Külker
date: 2023-01-14
version: 0.1.1
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
- Mail::MtPolicyd
- policyd-rate-limit
- postfix-policyd-spf-perl
- postfix-policyd-spf-python
commands:
- policyd-weight
- aptitude
packages:
- policyd-weight
- policyd-rate-limit
- postfix-policyd-spf-perl
- postfix-policyd-spf-python
- memcached
- mtpolicyd
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

| Name                           | License          | Deb | Lang   | Updated | Note     |
| ------------------------------ | ---------------- | --- | ------ | ------- | -------- |
| [Mail::MtPolicyd][]            | GPL2             | yes | Perl   | 2020    |          |
| [policyd-rate-limit][]         | GPL3             | yes | Python | 2022    | Rate     |
| [Policy Weight][]              | GPL2             | yes | Perl   | 2009    | MX, HALO |
| [postfix-policyd-spf-perl][]   | GPL2             | yes | Perl   | 2018    | SPF      |
| [postfix-policyd-spf-python][] | GPL2, Apache 2.0 | yes | Python | 2022    | SPF      |

## MT Policy Daemon

[Mail::MtPolicyd]: https://metacpan.org/dist/Mail-MtPolicyd
[Mail::MtPolicyd Cookbook]: https://metacpan.org/dist/Mail-MtPolicyd/view/lib/Mail/MtPolicyd/Cookbook.pod
[Mail::MtPolicyd Debian package]: https://packages.debian.org/stable/mail/mtpolicyd
[Mail::MtPolicyd source code]: https://github.com/benningm/mtpolicyd
[Mail::MtPolicyd Debian popularity]: https://qa.debian.org/popcon.php?package=mtpolicyd

A modular policy daemon for postfix.

### Installation

`Mail::MtPolicyd` supports 3 connection:

1. `SQL`
2. Memcached
3. `LDAP`

```bash
aptitude install memcached mtpolicyd
```
### Links

- [Mail::MtPolicyd][]
- [Mail::MtPolicyd Cookbook][]
- [Mail::MtPolicyd Debian package][]
- [Mail::MtPolicyd source code][]
- [Mail::MtPolicyd Debian popularity][]

### Plugins

| Plugin                      | Note                                                  |
| --------------------------- | ----------------------------------------------------- |
| Accounting                  | accounting in sql tables                              |
| Action                      | returns an action                                     |
| AddScoreHeader              | adding the score as header to the mail                |
| ClearFields                 | unset session variables                               |
| Condition                   | conditions based on session values                    |
| CtIpRep                     | the Commtouch IP reputation service (ctipd)           |
| DBL                         | checking helo, sender domain, rdns against an DBL     |
| Eval                        | capture the output of plugins                         |
| Fail2Ban                    | block an address with fail2ban                        |
| GeoIPAction                 | checking geo information of an ip                     |
| GeoIPLookup                 | checking geo information of a client_address          |
| Greylist                    | greylisting mechanism with an auto whitelist          |
| Greylist::AWL::Base         | base class for grelisting AWL storage backends        |
| Greylist::AWL::Redis        | backend for redis greylisting awl storage             |
| Greylist::AWL::Sql          | backend for SQL greylisting awl storage               |
| Greylist::Ticket::Base      | base class for greylisting ticket storage backends    |
| Greylist::Ticket::Memcached | greylisting ticket storage backend for memcached      |
| Greylist::Ticket::Redis     | greylisting ticket storage backend for redis          |
| Honeypot                    | creating an honeypot                                  |
| LdapUserConfig              | retrieving per user configuration from LDAP           |
| PostfixMap                  | accessing a postfix access map                        |
| Proxy                       | forward request to another policy daemon              |
| Quota                       | accounting in sql tables                              |
| RBL                         | checking the client-address against an RBL            |
| RBLAction                   | checking the client-address against an RBL            |
| RegexList                   | regex matching                                        |
| Result                      | result returned by a plugin                           |
| Role::ConfigurableFields    | role for plugins using configurable fields            |
| Role::PluginChain           | role for plugins to support a nested plugin chain     |
| Role::Scoring               | role for plugins using scoring                        |
| Role::SqlUtils              | role with support function for plugins using sql      |
| Role::UserConfig            | role for plugins using per user/request configuration |
| SMTPVerify                  | remote SMTP address checks                            |
| SPF                         | apply SPF checks                                      |
| SaAwlAction                 | checking spamassassin AWL reputation                  |
| SaAwlLookup                 | querying a spamassassin AWL database for reputation   |
| ScoreAction                 | running an action based on the score                  |
| SetField                    | sets and key=value in the session                     |
| SqlList                     | accessing a SQL white/black/access list               |
| SqlUserConfig               | retrieving the user config of a user                  |
| Stress                      | postfix stress mode                                   |

## policyd-rate-limit

[policyd-rate-limit]: https://pypi.org/project/policyd-rate-limit/
[policyd-rate-limit Debian package]: https://packages.debian.org/buster/policyd-rate-limit
[policyd-rate-limit source code]: https://github.com/nitmir/policyd-rate-limit
[policyd-rate-limit Debian popularity]: https://qa.debian.org/popcon.php?package=policyd-rate-limit

Postfix `policyd` server allowing to limit the number of mails accepted by
postfix over several time periods, by `SASL` usernames and/or `IP` addresses,
limiting the number of mails a user can send.

### Links

- [policyd-rate-limit][]
- [policyd-rate-limit Debian package][]
- [policyd-rate-limit source code][]
- [policyd-rate-limit Debian popularity][]

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

## postfix-policyd-spf-perl

[postfix-policyd-spf-perl]: https://launchpad.net/postfix-policyd-spf-perl/
[postfix-policyd-spf-perl popularity]: https://qa.debian.org/popcon.php?package=postfix-policyd-spf-perl
[postfix-policyd-spf-perl Debian developer information]: https://tracker.debian.org/pkg/postfix-policyd-spf-perl
[postfix-policyd-spf-perl Debian package]: https://packages.debian.org/buster/postfix-policyd-spf-perl
[postfix-policyd-spf-perl source code]: https://git.launchpad.net/postfix-policyd-spf-perl

Simple Postfix policy server for `RFC 4408/7208` `SPF` checking.

### Links

- [postfix-policyd-spf-perl][]
- [postfix-policyd-spf-perl popularity][]
- [postfix-policyd-spf-perl Debian developer information][]
- [postfix-policyd-spf-perl Debian package][]
- [postfix-policyd-spf-perl source code][]

## postfix-policyd-spf-python

[postfix-policyd-spf-python]: https://launchpad.net/spf-engine
[postfix-policyd-spf-python popularity]: https://qa.debian.org/popcon.php?package=spf-engine
[postfix-policyd-spf-python Debian developer information]: https://tracker.debian.org/pkg/spf-engine
[postfix-policyd-spf-python Debian package]: https://packages.debian.org/buster/postfix-policyd-spf-python
[postfix-policyd-spf-python source code]: https://git.launchpad.net/spf-engine

Postfix policy server and `sendmail` milter called `spf-engine` for `SPF`
checking.

### Packages

- `postfix-policyd-spf-python`
- `pyspf-milter`
- `python3-spf-engine`

### Links

- [postfix-policyd-spf-python][]
- [postfix-policyd-spf-python popularity][]
- [postfix-policyd-spf-python Debian developer information][]
- [postfix-policyd-spf-python Debian package][]
- [postfix-policyd-spf-python source code][]

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2023-01-14 | mtpolicy policyd-rate-limit postfix-policyd-spf-perl |
| 0.1.0   | 2023-01-13 | Initial release Policy Weight                        |

