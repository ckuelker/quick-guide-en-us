---
title: Iperf
author: Christian Külker
date: 2021-06-05
version: 0.1.0
type: doc
disclaimer: True
TOC: True
categories:
- HPC
- Benchmark
commands:
- iperf
- iperf3
tags:
- Iperf
- Iperf3
description: Iperf and Iperf3 are tools to measure network bandwidth and other network  parameters

---

The [iperf] tool can be used to measure network performance. The current
version is [iperf3]. [Iperf] is a cross platform tool for active measurements
of the maximum achievable bandwidth on IP network protocols [TCP], [UDP] and
[SCTP] with IPv4 and IPv6. [iperf3] is a new implementation from scratch, with
the goal of a smaller, simpler code base, and a library version of the
functionality that can be used in other programs.

## Features

TCP and SCTP            | UDP
----------------------- | ----------------------
Bandwidth measurement   | creation of dedicated bandwidth streams
Report MSS/MTU size     | measure package loss
Observe MSS/MTU size    | measure delay jitter
Support TCP window size | use multicast

[iperf3] and specially [iperf3.1] has additional features compared to [iperf2]. However the following features
are not available:

- -d/-r: Bidirectional testing
- -I: Data transmitted from stdin
- -T: Time-to-live (TTL), for multicast
- -x: Exclude C(connection) D(data) M(multicast) S(settings) V(server) reports
- -y: Report as a Comma-Separated Values
- -C: Compatibility mode allows for use with older version of [iPerf]

## Debian Versions

Debian      | iperf          | iperf3
----------- | -------------- | ------
Stretch 9   | 2.0.9+dfsg1-1  | 3.1.3-1
Buster 10   | 2.0.12+dfsg1-2 | 3.6-2

## Installation of Iperf 2.x

```shell
aptitude install iperf
```

## Installation of Iperf 3.x

```shell
aptitude install iperf3
```

## Command Line Completion

For command line completion `bash` can be used. If there is the file
`/usr/share/bash-completion/completions/iperf`, most likely completion will
work for `bash`.

## Mailinglist

- [Mailing list]
- [Mailing list archive]

## Usage Of Iperf

[iperf] is a client  and server application and used on two computers. One
takes the role as server the other the role as client.  [iperf] then send
packages over the network and measure the link or links in between the server
and the client. Usually on would limit the link count between server and client
to understand which link has what performance. Many operations are similar
between [iperf2] and [iperf3].

### Simple Iperf2 Example

This is a simple [TCP] client server bandwidth test.

Server:

```shell
iperf -s
```

Client:

```shell
iperf -c 192.168.168.32
------------------------------------------------------------
Client connecting to 192.168.168.32, TCP port 5001
TCP window size: 85.0 KByte (default)
------------------------------------------------------------
[  3] local 192.168.168.33 port 42152 connected with 192.168.168.32 port 5001
[ ID] Interval       Transfer     Bandwidth
[  3]  0.0-10.0 sec  1.09 GBytes   939 Mbits/sec
```

### Simple Iperf3 Example

This is a simple [TCP] client server bandwidth test.

Server:

```shell
iperf3 -s
```

Client:

```shell
iperf3 -c 192.168.168.32
Connecting to host 192.168.168.32, port 5201
[  5] local 192.168.168.33 port 53614 connected to 192.168.168.32 port 5201
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-1.00   sec   113 MBytes   951 Mbits/sec    0    378 KBytes
[  5]   1.00-2.00   sec   111 MBytes   931 Mbits/sec    0    428 KBytes
[  5]   2.00-3.00   sec   112 MBytes   944 Mbits/sec    0    455 KBytes
[  5]   3.00-4.00   sec   112 MBytes   938 Mbits/sec    0    455 KBytes
[  5]   4.00-5.00   sec   112 MBytes   938 Mbits/sec    0    455 KBytes
[  5]   5.00-6.00   sec   111 MBytes   930 Mbits/sec    0    455 KBytes
[  5]   6.00-7.00   sec   112 MBytes   943 Mbits/sec    0    477 KBytes
[  5]   7.00-8.00   sec   112 MBytes   942 Mbits/sec    0    477 KBytes
[  5]   8.00-9.00   sec   111 MBytes   933 Mbits/sec    0    477 KBytes
[  5]   9.00-10.00  sec   111 MBytes   934 Mbits/sec    0    477 KBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-10.00  sec  1.09 GBytes   938 Mbits/sec    0             sender
[  5]   0.00-10.00  sec  1.09 GBytes   937 Mbits/sec                  receiver

```

## Links

- [documentation]
- [home ESnet]
- [home]
- [IP]
- [iperf2]
- [iperf3]
- [iperf3.1]
- [Mailing list]
- [Mailing list archive]
- [SCTP]
- [source iperf3]
- [TCP]
- [UDP]

[documentation]: https://iperf.fr/iperf-doc.php
[home ESnet]: https://iperf.fr/
[home]: http://software.es.net/iperf/
[IP]: https://en.wikipedia.org/wiki/Internet_Protocol
[iperf]: http://software.es.net/iperf/
[iperf2]: https://sourceforge.net/projects/iperf2/
[iperf3]: http://software.es.net/iperf/
[iperf3.1]: http://software.es.net/iperf/
[Mailing list]: https://sourceforge.net/projects/iperf/lists/iperf-users
[Mailing list archive]: https://sourceforge.net/p/iperf/mailman/iperf-users/
[SCTP]: https://en.wikipedia.org/wiki/Stream_Control_Transmission_Protocol
[source iperf3]: https://github.com/esnet/iperf
[TCP]: https://en.wikipedia.org/wiki/Transmission_Control_Protocol
[UDP]: https://en.wikipedia.org/wiki/User_Datagram_Protocol

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2021-06-05 | Initial release                                      |




