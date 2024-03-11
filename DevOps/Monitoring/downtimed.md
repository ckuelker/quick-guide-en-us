---
title: Downtimed
author: Christian Külker
date: 2024-03-11
version: 0.1.1
type: doc
disclaimer: True
toc: True
categories:
- Monitoring
tags:
- Downtime
commands:
- downtimed
- downtimes
- downtime
description: System downtime monitoring and reporting tool

---

## Introduction

`downtimed` is a software tool designed for monitoring and recording operating
system downtime, uptime, shutdowns, and crashes.

The `downtimed` daemon initiates at system startup, logging information about
the operating system's last downtime to a log destination and a database. This
data is accessible using the `downtimes` command.

Once initialized, `downtimed` operates in the background, periodically updating
a time-stamp file on the disk. This time-stamp helps estimate the last active
period of the system. During a proper system shutdown, `downtimed` records a
separate time-stamp. These records are utilized for reporting in subsequent
daemon starts.

`downtimes` is a command-line interface for reviewing downtime history stored
in the database.

The software is compatible with GNU/Linux, FreeBSD, NetBSD, OpenBSD, Mac OS X,
and Solaris operating systems and can be adapted for other UNIX-like systems.
`downtimed` is distributed under the _Simplified BSD license_.

## Installation

To install `downtimed`, use the following command:

```bash
aptitude install downtimed
```

This command installs the following components:

- Documentation (changelogs, README files)
- System service configuration for `systemd` and `init.d`
- Database storage location
- Executable and man page

The following files are part of the package:

~~~
/usr/share/doc/downtimed/changelog.gz
/lib/systemd/system/downtimed.service
/etc/init.d/downtimed
/etc/default/downtimed
/usr/share/doc/downtimed/README.Debian
/usr/share/doc/downtimed/README.md.gz
/var/lib/downtimed
/usr/sbin/downtimed
/usr/share/doc/downtimed/copyright
/usr/share/man/man8/downtimed.8.gz
/usr/share/doc/downtimed/changelog.Debian.gz
/usr/share/doc/downtimed/TODO
/usr/share/doc/downtimed
~~~

To check the status of the `downtimed` service, use:

```bash
systemctl status downtimed.service
● downtimed.service - Downtime record keeper
     Loaded: loaded (/lib/systemd/system/downtimed.service; enabled; preset: \
             enabled)
     Active: active (running) since Mon 2024-03-11 12:40:45 CET; 3min 24s ago
    Process: 12849 ExecStart=/usr/sbin/downtimed $DOWNTIMED_OPTS \
             (code=exited, status=0/SUCCESS)
   Main PID: 12850 (downtimed)
      Tasks: 1 (limit: 9402)
     Memory: 192.0K
        CPU: 4ms
     CGroup: /system.slice/downtimed.service
             └─12850 /usr/sbin/downtimed
```

## Usage

Initially, if the system has not experienced downtime, attempts to query
downtime records using `downtime` or `downtimes` will result in error messages
indicating the absence of the downtime database file.

```bash
downtime
downtime: can not open /var/lib/downtimed/downtimedb: No such file or directory

downtimes
downtimes: can not open /var/lib/downtimed/downtimedb: No such file or directory
```

After a reboot 31s of downtime can be seen.

```bash
downtime
down  2024-03-11 13:05:01 -> up 2024-03-11 13:05:32 =    00:00:31 (31 s)
```

Both commands have equal output. However after a second reboot `downtime`
and `downtimes` differ.

```bash
downtime
down  2024-03-11 13:07:41 -> up 2024-03-11 13:08:11 =    00:00:30 (30 s)

downtimes
down  2024-03-11 13:05:01 -> up 2024-03-11 13:05:32 =    00:00:31 (31 s)
down  2024-03-11 13:07:41 -> up 2024-03-11 13:08:11 =    00:00:30 (30 s)
```

## Links

- Official website: <https://dist.epipe.com/downtimed/>
- Source code repository: <https://github.com/snabb/downtimed>

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2024-03-11 | Release to GitHub, add usage section                 |
| 0.1.0   | 2021-02-16 | Initial release                                      |

