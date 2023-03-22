---
title: Ntpd
author: Christian Külker
date: 2023-03-22
version: 0.1.2
type: doc
disclaimer: True
toc: True
categories:
- Time
commands:
- ntpd
tags:
- Ntpd
description: Network Time Protocol (NTP) information and usage

---

A system's time can be set using various commands. The network time protocol
daemon (ntpd) helps to manage the time and keep it current.

## What is a Stratum?

Time servers are divided into classes. The highest class is 0 and is called
__stratum 0__. A stratum is an artificially created bed or layer. Stratum 0
time servers are usually time servers that have atomic clocks, GPS clocks, or
radio clocks. The next level of time servers is stratum 1, which takes its time
from the stratum 0 server. This goes down to stratum 16. These time servers do
not propagate time, but set the local clock.

## What is the Best Practice Regarding NTP?

- Standardize to Coordinated Universal Time (UTC). Standardize all systems
  within an organization to Coordinated Universal Time (UTC). Standardizing to
  UTC simplifies the correlation of log entries within the organization and
  with external parties, regardless of the time zone of the device being
  synchronized.

- Run a higher tier (stratum) time server (for example, stratum 0, stratum 1,
  ...) within the organization. Either with a local clock source or with access
  to an external stratum 0 time server. This is to avoid multiple time daemons
  competing for access to external stratum 0 servers.

- Do not run __two__ independent stratum 0 (or 1) time servers within the same
  organization. If you are running multiple stratum 0 or stratum 1 servers, use
  an __odd__ number of servers. That is, run three or more servers.

- If possible, run servers in geographically separate locations.

- Secure the network time service and server. Use a dedicated machine or
  machines without load. Restrict the commands that can be used on the
  high-level stratum servers. Do not allow public queries to the primary high
  stratum servers. Only allow known networks/hosts to communicate with their
  respective high stratum servers.

- UTC time is no longer considered a secret. While it is possible to add
  encryption to `NTP`, it comes with extended key management and as such is
  more likely to fail. While security is considered a good feature, it may
  cause more problems than benefits for `NTP`. Consider this carefully.

- Set up your own hierarchical `NTP` service for your local network.

- While it is possible to run a `NTP` server on a Raspberry Pi, consider a
  server with a UPS and battery buffered real-time clock. While the Raspberry
  Pi might work fine, it will lose accuracy on power loss. For a time server,
  high uptime is appreciated.

## How to Set the Time via Ntpd Manually?

When the battery of the real time clock is changed, or for other reasons (for
example, if the system does not have a buffered clock), the on-board time
differs greatly from the real time. Normally `ntpd` refuses to set this.
However, it is possible to force `ntpd` to set it from the network.

The old and short way is:

```bash
/etc/init.d/ntp stop
ntpd -gq
/etc/init.d/ntp start
```

The `-g` option is the same as `--panicgate` and allows a to set a time much
different from the current time. While `-q` (or `--quit`) sets the time once
and quits. The "modern" way is:

```bash
systemctl stop ntp
ntpd --panicgate --quit
systemctl start ntp
```

If you don't have network access, use `date -s 'FORMAT'` to set the time. For
example, `FORMAT` can be `2021-06-03T14:22:12`.

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2023-03-22 | Improve writing, fix version, typos                  |
| 0.1.1   | 2022-06-11 | Shell->bash, +history                                |
| 0.1.0   | 2021-06-03 | Initial release                                      |

