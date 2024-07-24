---
title: Monitoring Overview
linkTitle: Overview
author: Christian Külker
date: 2024-07-24
version: 0.1.9
type: doc
disclaimer: True
toc: True
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

| Software      | Language/Dependency    | Query | Deb | Kind  | License   |
| ------------- | ---------------------- | ----- | --- | ----- | --------- |
|  CACTI        | PHP5 Mysql Perl Apache |       | yes |       | GPL       |
|  CENTREON     |                        |       |     |       |           |
|  CHECKMK      | Python,C++,Shell,C,JS  | Nag.  | no  |       | GPLv2     |
|  GANGLIA      | C, Perl, PHP, Python   |       | yes |       |           |
|  GRAFANA      | Go, TypeScript, Shell  |       | no  | Usage | AGPL-v3   |
|  ICINGA       | Perl                   | Nag.  | yes | Mon.  | GPLv2/3   |
|  ICINGA2      | C++, web: PHP          | Nag.  | yes | Mon.  |           |
|  LIBRENMS     | PHP,MySQL              | SNMP  | no  |       | GPLv3     |
|  MANTIS       |                        |       |     |       |           |
|  MRTG         | Perl                   | SNMP  | yes | Usage |           |
|  MUNIN        | Perl httpd             |       | yes | Usage |           |
|  NAV          |                        |       |     |       | GPLv3     |
|  NETDATA      | C, Go, JavaScript      |       | yes |       | GPLv3     |
|  NETDISCO     | Perl, Javascript       |       |     |       | BSD3      |
|  NETSAINT     |                        |       |     |       |           |
|  NETXMS       | Java, C++, C           |       | no  |       | LGPL/GPL  |
|  NAGIOS3      | Perl                   | Nag.  | yes | Mon.  |           |
|  OBSERVIUM    | PHP                    | SNMP  | no  |       | Observium |
|  OPENNMS      | Java                   |       | no  |       | AGPLv3    |
|  OPSVIEW      |                        |       |     |       |           |
|  OP5/NINJA    |                        |       |     |       |           |
|  OP5/MONITOR  |                        |       |     |       |           |
|  PROMETHEUS   | Go, TypeScript, Shell  |       | yes | Mon.  | Apache-v2 |
|  SHINKEN      | Phython                | Nag.  | yes | Mon.  | AGPL-v3   |
|  THRUK        |                        |       |     |       |           |
|  ZABBIX       | C PHP                  |       | yes |       | GPLv2     |

    Nag.  - Nagios query type, including nrpe
    Usage - Tool display usage as target, no warnings
    Mon.  - Tool monitors, give warnings

### Links 🡺

- CACTI
  - Home <https://www.cacti.net/>
- CENTREON
- CHECKMK
  - Home <https://checkmk.com/>
  - Source <https://github.com/tribe29/checkMK>
- GANGLIA
  - Home <http://www.ganglia.info>
- GRAFANA
  - Home <https://grafana.com/>
  - Source <https://github.com/grafana/grafana>
- ICINGA, ICINGA2
  - Home <https://www.icinga.org/>
  - Company <https://icinga.com>
  - Source icinga2 <https://github.com/Icinga/icinga2>
  - Source icinga2-web <https://github.com/Icinga/icingaweb2>
  - Source icinga2-puppet <https://github.com/Icinga/puppet-icinga2>
- LIBRENMS
  - Home <https://www.librenms.org/>
  - Source <https://github.com/librenms/librenms>
- MANTIS
- MRTG
  - Home <http://oss.oetiker.ch/mrtg/>
- MUNIN
  - Home <http://munin-monitoring.org/>
- NAV
  - Home <https://nav.uninett.no/>
- NETSAINT
- NETXMS
  - Home <https://netxms.com/>
  - Source <https://github.com/netxms/netxms>
- NAGIOS3, NAGIOS4
  - Home <https://www.nagios.org/>
  - Source Nagios 4 nagios4-nagioscore <https://github.com/NagiosEnterprises/nagioscore>
- NETDISCO
  - Home <https://netdisco.org/>
  - Source <https://github.com/netdisco/netdisco>
- NETDTATA
  - Home <https://www.netdata.cloud/>
  - Source <https://github.com/netdata/netdata>
- OBSERVIUM
  - Home <http://www.observium.org/>
- OPENNMS
  - Home <https://www.opennms.com/>
  - Source <https://github.com/OpenNMS/opennms>
- OPSVIEW
- OP5/NINJA, OP5/MONITOR
- PROMETHEUS
  - Home <https://prometheus.io/>
  - Source <https://github.com/prometheus/prometheus>
- SHINKEN
  - Home <http://www.shinken-monitoring.org/>
  - Source <https://github.com/naparuba/shinken>
- THRUK
-  ZABBIX
  - Home <http://zabbix.com/>

## Web Interfaces

| Software | Application                  | URL                         |
| -------- | ---------------------------- | --------------------------- |
| Thruk    | Naemon Icinga Shinken Nagios | 🡺 <https://www.thruk.org/>  |

## Conference

| Software           | URL                                          |
| ------------------ | -------------------------------------------- |
| icinga             | 🡺 <https://icingaconf.com/>                  |

## Additional information

* [2015: nagios, icinga2, sensu](https://phillbarber.blogspot.com/2015/03/nagios-vs-sensu-vs-icinga2.html)
* [2016: 9 reasons not to install Nagios](https://pandorafms.com/blog/9-reasons-not-to-install-nagios-in-your-company/)
* [2017-German: nagios, icinga2,...](https://www.netways.de/en/blog/2017/01/25/icinga-nagios-naemon-omd-check_mk-op5-oder-shinken-teil-ii/)

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.9   | 2024-07-24 | +NETDISCO                                            |
| 0.1.8   | 2024-07-23 | +NETXMS                                              |
| 0.1.7   | 2024-07-22 | Reformat, -M/MONIT                                   |
| 0.1.6   | 2023-01-04 | +PROMETHEUS+GRAFANA, mod CHECK_MK                    |
| 0.1.5   | 2021-05-19 | Formatting for Quick-Guide                           |
| 0.1.4   | 2020-02-14 | comparison links                                     |
| 0.1.3   | 2016-07-02 | +NETSAINT to overview                                |
| 0.1.2   | 2016-07-01 | +MANTIS to overview                                  |
| 0.1.1   | 2016-06-29 | Improve overview                                     |
| 0.1.0   | 2016-06-22 | Initial version with overview                        |
