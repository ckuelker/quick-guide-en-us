---
title: Ntpd
author: Christian Külker
date: 2022-06-11
version: 0.1.1
type: doc
disclaimer: True
TOC: True
categories:
- Time
commands:
- ntpd
tags:
- Ntpd
description: Network Time Protocol information and usage

---

The time of a system can be adjusted by various commands. The network time
protocol daemon (ntpd) helps in managing the time and in helping to keep the
time up to date.

## What Is A Stratum?

Time servers are divided into classes. The highest class is 0 and is called
__stratum 0__. A stratum is a bed or layer artificially made. Stratum 0 time
servers are usually time servers that have atomic clocks, GPS clocks or radio
clocks. The next level is the stratum 1 time servers that take their time from
the stratum 0 server. This goes down to stratum 16. This time servers do not
propagate the time, but set the local clock.

## What Is The Best Practice regarding NTP?

- Standardize to Coordinated Universal Time (UTC) time. Within an organization,
  standardize all systems to coordinated universal time (UTC). Standardizing to
  UTC simplifies log entry correlation within the organization and with
  external parties no matter what time zone the device being synchronized is
  located in.

- Run a higher stratum time server (for example stratum 0, stratum 1, ...)
  within the organization. Either with a local clock source or with access to
  an external stratum 0 time server. This is to avoid competing numerous time
  daemons for access to external stratum 0 servers.

- Do not run __two__ independent stratum 0 (or 1) time serves within the
  organization. When running multiple stratum 0 or stratum 1 servers, use an
  __odd__ number of servers. So run three or more servers.

- If possible run servers in geographical distinct locations.

- Secure the network time service and server. Use a dedicated machine or
  machines without load. Restrict the commands that can be used on the high
  stratum servers.  Do not allow public queries of the primary high stratum
  servers. Only allow known networks/hosts to communicate with their respective
  high stratum servers.

- As of now the UTC time is not considered a secret. While it is possible
  to add encryption to `NTP`, it comes with extended key management and as
  such is more likely to fail. While security is considered a good quality,
  for `NTP` it might introduce more problems than benefits. Consider carefully.

- Setup your own hierarchical `NTP` service for your local network.

- While it is possible to run a `NTP` sever on a Raspberry Pi, consider a
  server with UPS and battery buffered real time clock. While the Raspberry
  Pi might work fine, he will loose the accuracy on power loss. For a time
  server a high uptime is appreciated.

## How To Set The Time via Ntpd Manually?

When changing the real time clock battery, or for some other reasons (if the
system has no buffered clock for example), the on board time differs much from
the real time. Usually `ntpd` refused to set this. However it possible to force
`ntpd` to set from the network.

The old and short way is:

```bash
/etc/init.d/ntp stop
ntpd -gq
/etc/init.d/ntp start
```

The option `-g` is the same as `--panicgate` and allow a to set a time which is
much different than the current time. While `-q` (or `--quit`) sets the time
once and quit. The "modern" way is:

```bash
systemctl stop ntp
ntpd --panicgate --quit
systemctl start ntp
```

If you do not have a network access use `date -s 'FORMAT'` to set the time.
`FORMAT` can be for example `2021-06-03T14:22:12`.

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2022-06-11 | Shell->bash, +history                                |
| 0.1.0   | 2021-06-03 | Initial release                                      |


