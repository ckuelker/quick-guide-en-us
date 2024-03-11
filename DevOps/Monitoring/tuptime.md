---
title: tuptime
author: Christian Külker
version: 0.1.0
date: 2024-03-11
type: doc
locale: en_US
lang: en
disclaimer: True
toc: True
categories:
- Monitoring
commands:
- ls
tags:
- tuptime
discription: Uptime and downtime summary

---

## Introduction

Tuptime is a software tool designed for reporting both historical and
statistical real-time operating data of a system, maintaining this information
across restarts. It offers a similar functionality to the 'uptime' command, but
provides a more comprehensive output. The tool summarizes both uptime and
downtime.

Tuptime operates without requiring daemon processes. It executes only during
system startup and shutdown processes initiated by the init manager. To address
unexpected shutdowns, such as power failures, the tool includes a cron job and
a timer unit that periodically update the records. System administrators can
configure the update interval based on their specific system requirements.

The tool is developed in Python, utilizing commonly used modules and aiming for
minimal dependency, rapid execution, and ease of understanding and modification
for specific use cases.

Data is stored in a SQLite database, allowing access and utilization by other
software. The database specifications are detailed in tuptime-manual.txt.
Tuptime offers the functionality to output data in various formats, including
seconds, epoch time, and CSV, facilitating integration with other commands.

Tuptime's primary function is to track system startups and shutdowns,
presenting this information in a user-friendly format. It does not include
features like email alerts for milestones or limitations on record history. The
tool is also designed to avoid incorrect logging of false startups, a common
issue in scenarios like NTP adjustments, virtualized environments, high-load
servers, or clock resynchronization after suspend and resume cycles.

## Installation

To install use this command

```bash
aptitude install tupdime
```

Package files

```
/usr/share/doc/tuptime/tuptime-manual.txt.gz
/etc/cron.d/tuptime
/usr/share/doc/tuptime/changelog.gz
/usr/bin/tuptime
/usr/share/man/man1/tuptime.1.gz
/usr/share/doc/tuptime/copyright
/usr/share/doc/tuptime
/lib/systemd/system/tuptime-sync.service
/lib/systemd/system/tuptime.service
/etc/init.d/tuptime
/lib/systemd/system/tuptime-sync.timer
```

## Usage


__Example 1: Default Output__

Command to display default output:

```bash
# Without data
System startups:         1  since  01:08:12 PM 03/11/2024
System shutdowns:        0 ok  +  0 bad
System life:             21m 54s

Longest uptime:          21m 54s  from  01:08:12 PM 03/11/2024
Average uptime:          21m 54s
System uptime:           100.0%  =  21m 54s

Longest downtime:        0s
Average downtime:        0s
System downtime:         0.0%  =  0s

Current uptime:          21m 54s  since  01:08:12 PM 03/11/2024
```

This output is right after installation, so basically it shows no data.

```bash
# With one reboot
System startups:         2  since  01:08:12 PM 03/11/2024
System shutdowns:        1 ok  +  0 bad
System life:             1h 11m 42s

Longest uptime:          1h 10m 12s  from  01:08:12 PM 03/11/2024
Average uptime:          35m 36s
System uptime:           99.33%  =  1h 11m 13s

Longest downtime:        29s  from  02:18:24 PM 03/11/2024
Average downtime:        29s
System downtime:         0.67%  =  29s

Current uptime:          1m 1s  since  02:18:53 PM 03/11/2024
```


__Example 2: Tabular Output__

Command to display output in table format:

```
# Without data
tuptime -t
No.               Startup T.    Uptime   Shutdown T.   End   Downtime

  1   01:08:12 PM 03/11/2024   23m 57s

# With 1 reboot
tuptime -t
  1   01:08:12 PM 03/11/2024   1h 10m 12s   02:18:24 PM 03/11/2024   OK         29s
  2   02:18:53 PM 03/11/2024        2m 5s
```

__Example 3: List Output__

Command to display output in list format:

```bash
# Without data
tuptime -l
Startup:  1  at  01:08:12 PM 03/11/2024
Uptime:   24m 3s

# With one reboot
Startup:  1  at  01:08:12 PM 03/11/2024
Uptime:   1h 10m 12s
Shutdown: OK  at  02:18:24 PM 03/11/2024
Downtime: 29s

Startup:  2  at  02:18:53 PM 03/11/2024
Uptime:   2m 36s

```

__Example 4: Including Kernel Information__

Command to include kernel information in the output:

```bash
# Without data
tuptime -k
System startups:    1  since  01:08:12 PM 03/11/2024
System shutdowns:   0 ok  +  0 bad
System life:        24m 7s
System kernels:     1

Longest uptime:      24m 7s  from  01:08:12 PM 03/11/2024
     ...kernel:      Linux-6.1.0-18-amd64-x86_64-with-glibc2.36
Average uptime:      24m 7s
System uptime:       100.0%  =  24m 7s

Longest downtime:     0s
       ...kernel:     None
Average downtime:     0s
System downtime:      0.0%  =  0s

Current uptime:       24m 7s  since  01:08:12 PM 03/11/2024
     ...kernel:       Linux-6.1.0-18-amd64-x86_64-with-glibc2.36
```

__Example 5: CSV Format Report__

Command to output report in CSV format:

```bash
# Without data
tuptime --csv
INFO:Version = 5.2.2
INFO:Arguments = {'at': None, 'bootid': False, 'csv': True, 'dtm_format': \
'%X %x', 'dec': 2, 'exclude': None, 'decp': None, 'db_file': \
'/var/lib/tuptime/tuptime.db', 'endst': 0, 'invert': False, 'kernel': False, \
'list': False, 'update': True, 'order': False, 'power': False, 'percentile': \
None, 'quiet': False, 'reverse': False, 'seconds': True, 'since': None, \
'table': False, 'tat': None, 'ts': None, 'tu': None, 'until': None, \
'verbose': True, 'silent': False}
INFO:System = Linux
INFO:Python = 3.11.2
INFO:Current locale = ('en_US', 'UTF-8')
INFO:Sys values = {'bootid': '24446bec-781b-4803-8b5d-712ae0b943a2', \
'btime': 1710158892, 'uptime': 3799, 'rntime': 3799, 'slptime': 0, \
'offbtime': None, 'downtime': None, 'kernel': \
'Linux-6.1.0-18-amd64-x86_64-with-glibc2.36'}
INFO:Execution user = 0
INFO:DB file exists = /var/lib/tuptime/tuptime.db
INFO:Getting DB connection
DEBUG:BEGIN deferred
DEBUG:PRAGMA user_version
INFO:DB user_version: 5
DEBUG:select rowid, bootid, btime, uptime, endst from tuptime where \
rowid = (select max(rowid) from tuptime)
INFO:Last DB values = {'rowid': 1, 'bootid': \
'24446bec-781b-4803-8b5d-712ae0b943a2', 'btime': 1710158892, 'uptime': 3710, \
'endst': 0, 'buptime': 1710162602}
INFO:System restarted = False from bootid
INFO:Drift over btime = 0
DEBUG:update tuptime set uptime =3799, rntime =3799, slptime =0, endst =0, \
kernel ='Linux-6.1.0-18-amd64-x86_64-with-glibc2.36' where rowid =1
DEBUG:COMMIT
INFO:DB info = update ok
DEBUG:select rowid as startup, * from tuptime
INFO:Refresh last row values = {'startup': 1, 'bootid': \
'24446bec-781b-4803-8b5d-712ae0b943a2', 'btime': 1710158892, 'uptime': 3799, \
'rntime': 3799, 'slptime': 0, 'offbtime': None, 'endst': 0, 'downtime': None, \
'kernel': 'Linux-6.1.0-18-amd64-x86_64-with-glibc2.36'}
"System startups","1","since","1710158892"
"System shutdowns","0","ok","+","0","bad"
"System life","3799"
"Longest uptime","3799","from","1710158892"
"Average uptime","3799"
"System uptime","100.0%","=","3799"
"Longest downtime","0"
"Average downtime","0"
"System downtime","0.0%","=","0"
"Current uptime","3799","since","1710158892"
```

__Example 6: Seconds and Epoch Time Format__

Change default human readable date-time/time-stamp style and print times in
seconds and date-times in epoch.

```bash
# Without data
tuptime -s
System startups:      1  since  1710158892
System shutdowns:     0 ok  +  0 bad
System life:          4030

Longest uptime:       4030  from  1710158892
Average uptime:       4030
System uptime:        100.0%  =  4030

Longest downtime:      0
Average downtime:      0
System downtime:       0.0%  =  0

Current uptime:        4030  since  1710158892

```

__Example 7: Custom Date-Time/Timestamp Format__

Change the date-time/time-stamp format. By default the output use the configured
system locales.

```bash
# Without data
tuptime -d '%H:%M:%S %m-%d-%Y'
System startups:      1  since  13:08:12 03-11-2024
System shutdowns:     0 ok  +  0 bad
System life:          1h 8m 53s

Longest uptime:       1h 8m 53s  from  13:08:12 03-11-2024
Average uptime:       1h 8m 53s
System uptime:        100.0%  =  1h 8m 53s

Longest downtime:     0s
Average downtime:     0s
System downtime:      0.0%  =  0s

Current uptime:       1h 8m 53s  since  13:08:12 03-11-2024
```

__Example 8: Reporting Since a Past Point in Time__

Command to report since a specified past time in epoch seconds:

```bash
# Without data
tuptime --tsince -31557600
System startups:      1  since  08:17:42 AM 03/12/2023
System shutdowns:     0 ok  +  0 bad
System life:          1h 9m 30s

Longest uptime:       1h 9m 30s  from  01:08:12 PM 03/11/2024
Average uptime:       1h 9m 30s
System uptime:        100.0%  =  1h 9m 30s

Longest downtime:     0s
Average downtime:     0s
System downtime:      0.0%  =  0s

Current uptime:       1h 9m 30s  since  01:08:12 PM 03/11/2024
```

## Links

- Source <https://github.com/rfmoz/tuptime>

## Alternatives

- journalctl --list-boots
- [downtimed](downtimed.md)
- lastwake
- uptimed

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2024-03-11 | Initial release                                      |

