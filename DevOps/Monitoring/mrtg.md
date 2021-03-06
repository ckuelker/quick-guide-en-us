---
title: MRTG on Raspberry Pi
linkTitle: MRTG
author: Christian Külker
date: 2021-05-18
version: 0.1.5
type: doc
disclaimer: True
TOC: True
categories:
- Monitoring
commands:
- snmpwalk
tags:
- MRTG
- Raspberry PI
- Apache
- Nginx
- SNMP
description: Monitoring SNMP network devices and draw pretty pictures showing how much traffic has passed through each interface.

---

## Introduction

This article is about a software called Multi Router Traffic Grapher (MRTG)
that can monitor the load of network interfaces and other values on one ore
more computers.  While this article tries to be as explicative as possible due
to the nature of the subject it is targeting experienced Linux users or system
administrators.

This article describe a simple installation on one server. In fact a small
Raspberry Pi router. It should be very similar with any Debian based
distribution on other hardware too.

One advantage of using [MRTG] is that it explicitly summarise the usage of
network devices on daily, weekly and monthly basis in terms of **speed**. This
is useful, if you are connected with a limited internet connection and you need
to to know how fast your are.

## Dependencies

The [MRTG] is usually a multi server installation. One server can be a web
server that collects and display aggregated log data. Other servers or switches
can be queried via **[SNMP]**. For this article everything is installed on one
machine.

### Web Server

One dependency for [MRTG] is the web server. That can basically any web server.
Out of curiosity I tested **Nginx** (speak: engine x) and it works. However in
the past, when [MRTG] was created, Apache was more common. For this reason many
guides on the web have some tricks when it comes with the usage of Apache. You
might consider [Apache2], if you need certain features.

#### Configuring of Nginx for MRTG

The installation is straight forward:

    aptitude update
    aptitude install nginx

Copy the following into the file `/etc/nginx/sites-available/mrtg`

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
server {
        listen 8080 default_server;
        listen [::]:8080 default_server;

        root /var/www/mrtg;
        index index.html;
        server_name _;
        location /mrtg/ {
                try_files $uri $uri/ =404;
        }
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Then make a link and restart **Nginx**

    cd /etc/nginx/sites-eanabled
    ln -s /etc/nginx/sites-available/mrtg .
    service nginx restart

#### Configuration of Apache2 for MRTG

The configuration can be done in many ways. [MRTG] can be configured as main
host, virtual host with and without SSL. The following configuration is using
the Debian default `site configuration` and do not set up [MRTG] as a site,
but as an `configuration snippet` for [Apache2].

Copy the following content into the file
`/etc/apache2/conf-available/mrtg.conf`.  The values for `AllowOverride` and
`Options` need to be made specific for the server. This is just an example.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  <IfModule mod_alias.c>
    Alias /mrtg   /var/www/mrtg
  </IfModule>

  <Directory /var/www/mrtg/>
    Options SymLinksIfOwnerMatch
    AllowOverride None
  </Directory>

  ErrorLog  /var/log/apache2/mrtg-error.log
  CustomLog /var/log/apache2/mrtg-access.log combined
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This kind of configuration can be enabled by `a2enconf mrtg`.

### SNMP Server

Install the **[SNMP]** server and tools.

    aptitude install snmp snmpd

Check if the server is running with one (!) of the following commands.

     ps ax|grep snmp|grep -v grep
     service snmpd status

The first command should give something like:

    29529 ?        S      0:00 /usr/sbin/snmpd -Lsd -Lf /dev/null -u snmp \
    -g snmp -I -smux mteTrigger mteTriggerConf -p /run/snmpd.pid

Test the server. The following command should give some output

    snmpwalk -v1 -cpublic localhost

    iso.3.6.1.2.1.1.1.0 = STRING: "Linux pi 4.4.9-v7+ #884 SMP Fri \
    May 6 17:28:59 BST 2016 armv7l"
    iso.3.6.1.2.1.1.2.0 = OID: iso.3.6.1.4.1.8072.3.2.10
    iso.3.6.1.2.1.1.3.0 = Timeticks: (38719) 0:06:27.19
    iso.3.6.1.2.1.1.4.0 = STRING: "Me <me@example.org>"
    iso.3.6.1.2.1.1.5.0 = STRING: "pi"
    iso.3.6.1.2.1.1.6.0 = STRING: "Sitting on the Dock of the Bay"
    iso.3.6.1.2.1.1.7.0 = INTEGER: 72
    ...

As it can be seen from this output some values in ``/etc/snmp/snmpd.conf`` are
wrong. The e-mail address and the location "Sitting on the Dock of the Bay".
However for a basic function a change it is not needed. After your complete
installation works, make a backup of that configuration file and update
missing/ wrong entries.

Especially under Debian the problem is a very limiting default configuration
that do not give read access to most of the MIB tree. To change this, for
example, the following `-V systemonly` has to be removed from `rocommunity
public  default` for [IPv4] in `/etc/snmp/snmpd.conf` and the `snmpd` service
has to be restarted. If one uses [IPv6] a different line need to be changed.

```shell
# before
snmpwalk  -v 2c -c public localhost |wc -l
47
# after
snmpwalk  -v 2c -c public localhost |wc -l
8936
```

## Installation of MRTG

The installation of [MRTG] is easy:

```shell
aptitude install mrtg
```

## MRTG Configuration

The first step is to secure the default configuration. The reason to reuse the
name `mrtg.cfg` is, that `crond` or a `systemd timer` is most likely already
configured to use this fil, so we do not need to modify `crond` or `systemd`.
For Debian 10 Buster [MRTG] uses cron in `/etc/cron.d/mrtg` and updates the
graphs every 5 minutes and write log entries to `/var/log/mrtg/mrtg.log`. You
should check the log file for errors in case the configuration is updated.

```shell
cp -a /etc/mrtg.cfg /etc/mrtg.cfg.`date +'%F'`
```

The configuration of the web interface is a very crude and archaic process,
compared to INI or YAML files. The configuration is done via `/etc/mrtg.cfg`
and applied by a command since the `mrtg` command is executed via a scheduler:
`crond` or `systemd`. The format of [mrtg.cfg] can be looked up via the
[mrtg-reference].

### Adding Entries/ Graphs

Most entries are added via [SNMP] OIDs to the file [mrtg.cfg]. There fore
looking up the MIB and figuring out which value can be reached via `snmpget` is
the first thing to do.

A graph entry can be generated by a set of configuration keywords belonging to
one entity. Lets call the entity 'hobbit' for example, than it looks in an
abstract way like this:

```
KeyWord1[hobbit]: Value1
KeyWord2[hobbit]: Value2
KeyWord3[hobbit]: Value3
KeyWord4[hobbit]: Value4
KeyWord5[hobbit]: Value5
```

One of the most important keywords is the __Target__ keyword. The __value__ of
a keyword is called an __argument__ in [MRTG], probably because a keyword is
related to a command.  For the __Target__ keyword different commands can be
executed, which manifests it self in different __argument__ classes. So to
understand you have to reverse the thinking. The kind of value to a keyword
determines with command is executed. The following classes are defined for
the keyword __target__:

- Basic
- SNMPv2c
- SNMPv3
- noHC
- Reversing
- Explicid OIDs
- MIB variables
- Snmpwalk
- SnmpGetNext
- Counted SNMP Walk
- Interface by IP
- Interface by Description
- Interface by Name
- Interface by Ethernet Address
- Interface by Type
- Extended positioning of ifIndex

As this is quite extensive, only the __Explicid OIDs__ class is used. For this
to work **two** OIDs have to be used. The reason is that per default [MRTG]
plots 2 variables against time. Usually this will be network bytes in and out.
So, to plot a single value the same OID has to be used twice.

The format is:

~~~
Taget[NAME]: OID_1&OID_2:COMMUNITY@HOST

  NAME: arbitrary word
  OID_1: numerical SNMP OID
  OID_2: numerical SNMP OID
  COMMUNITY: SNMP community, for example 'public'
  HOST: hostname of machine to query, for example 'localhost'
~~~

The format can be more flexible. For all places where `COMMUNITY@HOST` can be
used the real format of this can be

```
COMMUNITY@HOST[:[port][:[timeout][:[retries][:[backoff][:[version]]]]][|name]
```

Also __port__ can be more flexible. See [mrtg-reference] for examples and
explanation.

What has to be understood is that the value of __Target__ is interpolated in a
very [MRTG] specific way and some expressions are extrapolated. So addition,
subtraction, division, multiplication, brackets and piping to custom commands
works. It is a complete language by itself.

When you find a valid OID, for example `.1.3.6.1.4.1.2021.4.6` for the total
amount of free main memory, you sometimes have to add a `0` to make [MRTG] and
[SNMP] happy: `.1.3.6.1.4.1.2021.4.6.0`.

A target for the value would look like this:

~~~
Target[hobbit]: .1.3.6.1.4.1.2021.4.6.0&.1.3.6.1.4.1.2021.4.6.0:public@localhost
~~~

The full example for the measurement of free main memory on a Debian 10 Buster
Linux Raspberry Pi 4 with 4 GB main memory looks like this:

~~~
# --- [ Global configuration ] ------------------------------------------------
WorkDir: /var/www/mrtg
WriteExpires: Yes
EnableIPv6: no

# --- [ Free Main Memory ] ----------------------------------------------------
Title[localhost-free]: Localhost free main memory
PageTop[localhost-free]: <H1>Localhost - Memory Free</H1>
Target[localhost-free]: .1.3.6.1.4.1.2021.4.6.0&.1.3.6.1.4.1.2021.4.6.0:\
public@localhost
MaxBytes[localhost-free]: 3918772
YLegend[localhost-free]: memory
ShortLegend[localhost-free]: Bytes
LegendI[localhost-free]: bytes
Legend1[localhost-free]: bytes free
Options[localhost-free]: integer, gauge, nopercent, growright, unknaszero, noo
~~~

The value for __MaxBytes__ can be queried with [SNMP]: (A working configuration
for `snmpd` is assumed here - if this does not give a value, either the `snmpd`
configuration need set up correctly or it is a different hardware)

```shell
snmpget -v 2c localhost -c public .1.3.6.1.4.1.2021.4.5.0
iso.3.6.1.4.1.2021.4.5.0 = INTEGER: 3918772
```

Then the configuration needs to be activated

```shell
LANG=C indexmaker /etc/mrtg.cfg > /var/www/mrtg/index.html
```

Then the configuration needs to be executed twice, as first run throws errors
due to an empty database.

```shell
LANG=C /usr/bin/mrtg  /etc/mrtg.cfg
LANG=C /usr/bin/mrtg  /etc/mrtg.cfg
```

#### Semi Automatic Configuration

A semi automatic configuration for network interfaces is possible with the
`cfgmaker` script.

Since one interface, the ``wlan0``, do not deliver a speed value, to be able to
use it with [MRTG], it is necessary to set a value with the ``-zero-speed=``
parameter

```shell
LANG=C cfgmaker --zero-speed=100000000 public@127.0.0.1  > /etc/mrtg.cfg
```

[MRTG] has a limited capability to scan hardware and create a configuration for
it.

```shell
LANG=C cfgmaker public@127.0.0.1 --ifref=descr --output /etc/mrtg.cfg
```

This basically generates 3 interface graphs for `lo`, `eth0` and `wlan` on the
Raspberry Pi 4. Some commented out. The `eth0` section looks like:

~~~
### Interface 2 >> Descr: 'eth0' | Name: 'eth0' | Ip: '192.168.168.35' | \
Eth: 'dc-a6-32-78-c1-d5' ###

Target[127.0.0.1_2]: 2:public@127.0.0.1:
SetEnv[127.0.0.1_2]: MRTG_INT_IP="192.168.168.35" MRTG_INT_DESCR="eth0"
MaxBytes[127.0.0.1_2]: 125000000
Title[127.0.0.1_2]: Traffic Analysis for 2 -- monitor
PageTop[127.0.0.1_2]: <h1>Traffic Analysis for 2 -- monitor</h1>
    <div id="sysdetails">
      <table>
        <tr>
          <td>System:</td>
          <td>monitor in monitor.c8i.org</td>
        </tr>
        <tr>
          <td>Maintainer:</td>
          <td>c  &lt;c@c8i.org&gt;</td>
        </tr>
        <tr>
          <td>Description:</td>
          <td>eth0  </td>
        </tr>
        <tr>
          <td>ifType:</td>
          <td>ethernetCsmacd (6)</td>
        </tr>
        <tr>
          <td>ifName:</td>
          <td>eth0</td>
        </tr>
        <tr>
          <td>Max Speed:</td>
          <td>125.0 MBytes/s</td>
        </tr>
        <tr>
          <td>Ip:</td>
          <td>192.168.168.35 (monitor.c8i.org)</td>
        </tr>
      </table>
    </div>
~~~

Even though the scanning understands that this is the `eth0` interface, the
name of the title is just the number '2'. Which might work well for switches,
but not for hosts. Clicking on the graph however show the information.

### Example Configuration For CPU Idle Time

This example show the configuration of the CPU idle time for the Raspberry PI 4
and shows how simple mathematical terms are realized inside the target keyword.

~~~
Title[localhost-CPU]: Localhost CPU load
PageTop[localhost-CPU]: <H1>Localhost - CPU Idle Time (%)</H1>
Target[localhost-CPU]: 100 - .1.3.6.1.4.1.2021.11.11.0&\
.1.3.6.1.4.1.2021.11.11.0:public@localhost
MaxBytes[localhost-CPU]: 100
YLegend[localhost-CPU]: CPU %
ShortLegend[localhost-CPU]: %
LegendI[localhost-CPU]: CPU
Legend1[localhost-CPU]: CPU usage
Options[localhost-CPU]: integer, gauge, nopercent, growright, unknaszero, noo
~~~

![CPU Idle Time](mrtg-localhost-cpu-idle-time-v0.1.0.png "CPU Idle Time")

### Configuring MRTG Web output

One can of course generate an index page by hand, which is probably
recommended. A quick and dirty approach is to use the `indexmaker` script.
This creates a page with one graph per target and a link to the sub-page of
the target.

    mkdir -p /var/www/mrtg
    LANG=C indexmaker /etc/mrtg.cfg > /var/www/mrtg/index.html

## Updating The Data For The Web Page

Manually:

    LANG=C mrtg

Of course this is already configured with `crond`.

## Access The New Installed MRTG

Point your web browser to the IP of your [MRTG] machine.

    http://<IP-TO-MRTG-HOST>/

Or

    http://<IP-TO-MRTG-HOST>/PATH

## Testing And Debugging

### SNMP And SNMPD

For many functionality of [MRTG] a working and well configured `snmpd` is
mandatory. Version 1 can be tested with:

```shell
snmpstatus -c public -v1 localhost
```

This would be considered an error for example:

```
Error in packet.
Reason: (noSuchName) There is no such variable name in this MIB.
Failed object: iso.3.6.1.2.1.4.10.0

Error in packet.
Reason: (noSuchName) There is no such variable name in this MIB.
Failed object: iso.3.6.1.2.1.4.3.0

[UDP: [127.0.0.1]:161->[0.0.0.0]:45109]=>[Linux monitor 5.4.79-v7l+ #1373 SMP \
Mon Nov 23 13:27:40 GMT 2020 armv7l] Up: 0:00:54.02
Interfaces: 0, Recv/Trans packets: 0/0 | IP: 0/0
```

Version 2c can be tested with

```shell
snmpstatus -c public -v2c localhost
```

This would considered a success:

```shell
[UDP: [127.0.0.1]:161->[0.0.0.0]:49633]=>[Linux monitor 5.4.79-v7l+ #1373 SMP \
Mon Nov 23 13:27:40 GMT 2020 armv7l] Up: 0:03:16.30
Interfaces: 0, Recv/Trans packets: 0/0 | IP: 0/0
```

To query the kernel on Debian 10 Buster on Raspberry PI 4 do

```shell
snmpget -v2c -cpublic localhost iso.3.6.1.2.1.1.1.0
iso.3.6.1.2.1.1.1.0 = STRING: "Linux monitor 5.4.79-v7l+ #1373 SMP Mon Nov \
23 13:27:40 GMT 2020 armv7l"
```

There are different approaches to query the memory, one is:

```shell
# UCD-SNMP-MIB::memTotalReal
snmpget -v2c -cpublic localhost .1.3.6.1.4.1.2021.4.5.0
iso.3.6.1.4.1.2021.4.5.0 = INTEGER: 3918772
```

### Run MRTG Manually

The cron job defined and runs [MRTG] as follows:

```shell
if [ -x /usr/bin/mrtg ] && [ -r /etc/mrtg.cfg ] && \
[ -d "$(grep '^[[:space:]]*[^#]*[[:space:]]*WorkDir' /etc/mrtg.cfg | \
awk '{ print $NF }')" ]; then mkdir -p /var/log/mrtg ; \
env LANG=C /usr/bin/mrtg /etc/mrtg.cfg 2>&1 | tee -a /var/log/mrtg/mrtg.log ; \
fi
```

This can be used to run [MRTG] immediately and there is no need to wait 5
minutes.

### Running MRTG With Different Log Information

```shell
LANG=C /usr/bin/mrtg -debug cfg /etc/mrtg.cfg
```
### Debugging SNMP

This helps to debug `snmpget` and other [SNMP] operations as well as some
script debugging.

```shell
LANG=C /usr/bin/mrtg -debug snpo /etc/mrtg.cfg
```

A script failure might look like

~~~
--snpo: run external sh /etc/mrtg/mrtg_ping 8.8.8.8
--snpo: External result:100 out:undef uptime:unknown name:unknown
~~~

However in this case the script do not return all values, so this
is actually intended.

## Packages

~~~
mrtg                - multi router traffic grapher
mrtg-contrib        - multi router traffic grapher (contributed files)
mrtg-ping-probe     - Ping module for Multi Router Traffic Grapher
mrtg-rrd            - Generating graphs for MRTG statistics (CGI)
mrtgutils           - Utilities to generate statistics for mrtg
mrtgutils-sensors   - Utilities to generate statistics for mrtg (from lm-sensors)
pcp-import-mrtg2pcp - Tool for importing data from MRTG into PCP archive logs
~~~

## Known Problems and Caveats

- The standard web page (index.html) of [MRTG] will not **reload** when [MRTG]
  has updated the graphs manually. The page refresh is set to the time
  specified inside the configuration, usually 10 minutes, which make sense.
  However when debugging or adding new configuration [MRTG] is usually run
  manually and the page do not update. To mitigate, press browser reload
  button.
- **Devices are numbers:** For now the display of the index page enumerates
  devices, like 2 (``eth0``) and 3 (``wlan0``) that is not very intuitive. I
  tried to use some options to ``cfgmaker`` but without success. In case you
  know the answer I would be happy to include it here.
- **No back links:** If you click on a device graph of the index page you can
  enter the detailed report about each device on a dedicated page. There are no
  links back. One can modify the configuration and add a link for every target
  manually in the `PageTop` attribute.
- Nice about [MRTG] is that it already have a daily, weekly, monthly and yearly
  overview of network interfaces **speed** (that can be generated
  automatically). However it do not have a summary on how much KB or MB or GB
  (or KiB, MiB, GiB) the interface processed over the time (of a day, month,
  week or year) aka **accumulated traffic**. So if your intention is to keep
  the usage of your mobile plan under control then [MRTG] will not help.

## Critique

__Pros__

- Simple architecture (configuration, cron, log file, web pages)
- Most data accessible via [SNMP] and scripts
- Easy testing of sensors
- Semi automatic setup of interface speed measurements
- Simple data model
- Moderate dependencies
- No obfuscating abstraction layers
- Reliable execution
- Low system resource usage

__Cons__

- First invocation of [MRTG] always produces errors (the handling of data files
  and data backup files is not clean)
- No back links in the web interface to the index page
- Mixing of HTML and configuration inside the configuration
- Difficult to configure new graphs from scratch
- The configuration is a complete new language
- 2 value constrains for single value graphs
- Only numeric values
- Data and markup language is not separated and even stored in log files inside
  the web root (e.g. `/var/www/mrtg/localhost-cpu.log`)

## Links

- [satsignal]
- [mrtg-rbp-project]

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.5   | 2021-05-18 | Updates for Raspberry PI 4                           |
| 0.1.4   | 2021-01-02 | Updates for Debian 10 Buster                         |
| 0.1.3   | 2020-06-06 | Formatting for Quick-Guide                           |
| 0.1.2   | 2016-06-25 | Add caveats section                                  |
| 0.1.1   | 2016-06-21 | Add Nginx configuration                              |
| 0.1.0   | 2016-06-20 | Initial release                                      |

[Apache2]: https://httpd.apache.org/
[IPv4]: https://en.wikipedia.org/wiki/IPv4
[IPv6]: https://en.wikipedia.org/wiki/IPv6
[mrtg]: https://oss.oetiker.ch/mrtg/
[mrtg.cfg]: https://oss.oetiker.ch/mrtg/doc/mrtg-reference.en.html
[mrtg-rbp-project]: https://github.com/rvdhoek/Raspberry-MRTG
[mrtg-reference]: https://oss.oetiker.ch/mrtg/doc/mrtg-reference.en.html
[OID]: https://en.wikipedia.org/wiki/Object_identifier
[satsignal]: https://www.satsignal.eu/raspberry-pi/monitoring.html
[SNMP]: https://en.wikipedia.org/wiki/Simple_Network_Management_Protocol


