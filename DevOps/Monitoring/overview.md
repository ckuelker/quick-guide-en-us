---
title: Monitoring Overview
linkTitle: Overview
author: Christian Külker
date: 2021-05-19
version: 0.1.5
type: doc
disclaimer: True
TOC: True
categories:
- Monitoring
tags:
- Overview
description: An overview regarding monitoring solutions, the foundation of system administration

---

## Introduction

Free Open Source Software has a long tradition in providing system monitoring
software. And this domain is still a stronghold for traditional programming
languages of system administrators, shell and Perl.

This overview distinguish basically two different kind of Monitoring software:
All purpose server monitoring and special device monitoring. Of course the
boarders are blurred. While MRTG started as a special network device monitoring
tool where the focus is to know what happened in the past.  For example how
much traffic has been seen last month.  This is marked as 'Usage'.  The other
solutions, like the Nagios orientated ones, focus more on the question if a
certain server is up or not and if the data of certain devices are in expected
ranges or not. This is marked as 'Mon.'.

## Overview


| Software    | Language/Dependency    | Query | Deb | Kind  | License   | URL                                                   |
| ----------- | ---------------------- | ----- | --- | ----- | --------- | ----------------------------------------------------- |
| CACTI       | PHP5 Mysql Perl Apache |       | yes |       | GPL       | [🡺](http://www.cacti.net/)                            |
| CENTREON    |                        |       |     |       |           |                                                       |
| CHECK_MK    |                        |       |     |       |           | [🡺](https://checkmk.com/)                             |
| GANGLIA     | C, Perl, PHP, Python   |       | yes |       |           | [🡺](http://www.ganglia.info)                          |
| ICINGA      | Perl                   | Nag.  | yes | Mon.  | GPLv2/3   | [🡺](https://www.icinga.org/) [🡺](https://icinga.com)  |
| ICINGA2     | C++, web: PHP          | Nag.  | yes | Mon.  |           | [🡺](https://www.icinga.org/) [🡺](https://icinga.com)  |
| MANTIS      |                        |       |     |       |           |                                                       |
| MRTG        | Perl                   | SNMP  | yes | Usage |           | [🡺](http://oss.oetiker.ch/mrtg/)                      |
| MUNIN       | Perl httpd             |       | yes | Usage |           | [🡺](http://munin-monitoring.org/)                     |
| NAV         |                        |       |     |       | GPLv3     | [🡺](https://nav.uninett.no/)                          |
| NETSAINT    |                        |       |     |       |           |                                                       |
| NAGIOS3     | Perl                   | Nag.  | yes | Mon.  |           | [🡺](https://www.nagios.org/)                          |
| OBSERVIUM   | PHP                    | SNMP  | no  |       | Observium | [🡺](http://www.observium.org/)                        |
| OPSVIEW     |                        |       |     |       |           |                                                       |
| OP5/NINJA   |                        |       |     |       |           |                                                       |
| OP5/MONITOR |                        |       |     |       |           |                                                       |
| SHINKEN     | Phython                | Nag.  | yes | Mon.  | AGPL v3   | [🡺](http://www.shinken-monitoring.org/)               |
| THRUK       |                        |       |     |       |           |                                                       |
| ZABBIX      | C PHP                  |       | yes |       | GPLv2     | [🡺](http://zabbix.com/)                               |

    Nag.  - Nagios query type, including nrpe
    Usage - Tool display usage as target, no warnings
    Mon.  - Tool monitors, give warnings

## Other

- [OpenNMS]
- [Prometheus] + [Grafana]
- [Netdata]
- M/Monit
- LibreNMS

[checkmk]: https://checkmk.com/
[Prometheus]: https://prometheus.io/
[Grafana]: https://grafana.com/grafana/
[OpenNMS]: https://www.opennms.org/en
[Netdata]: https://www.netdata.cloud/
[M/Monit]: https://mmonit.com/
[LibreNMS]: https://www.librenms.org/


## Web Interfaces

| Software | Application                  | URL                         |
| -------- | ---------------------------- | --------------------------- |
| Thruk    | Naemon Icinga Shinken Nagios | [🡺](https://www.thruk.org/) |

## Source

| Software           | Repository                                                |
| ------------------ | --------------------------------------------------------- |
| chekMK             | [github](https://github.com/tribe29/checkMK)              |
| icinga2            | [github](https://github.com/Icinga/icinga2)               |
| icinga2-web        | [github](https://github.com/Icinga/icingaweb2)            |
| icinga2-puppet     | [github](https://github.com/Icinga/puppet-icinga2)        |
| nagios4-nagioscore | [github](https://github.com/NagiosEnterprises/nagioscore) |
| Shinken            | [github](https://github.com/naparuba/shinken)             |

## Conference

| Software           | URL                                          |
| ------------------ | -------------------------------------------- |
| icinga             | [icingaconf](https://icingaconf.com/)        |

## Links

* [2015: nagios, icinga2, sensu](https://phillbarber.blogspot.com/2015/03/nagios-vs-sensu-vs-icinga2.html)
* [2016: 9 reasons not to install Nagios](https://pandorafms.com/blog/9-reasons-not-to-install-nagios-in-your-company/)
* [2017-German: nagios, icinga2,...](https://www.netways.de/en/blog/2017/01/25/icinga-nagios-naemon-omd-check_mk-op5-oder-shinken-teil-ii/)

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.5   | 2021-05-19 | Formatting for Quick-Guide                           |
| 0.1.4   | 2020-02-14 | comparison links                                     |
| 0.1.3   | 2016-07-02 | +NETSAINT to overview                                |
| 0.1.2   | 2016-07-01 | +MANTIS to overview                                  |
| 0.1.1   | 2016-06-29 | Improve overview                                     |
| 0.1.0   | 2016-06-22 | Initial version with overview                        |
