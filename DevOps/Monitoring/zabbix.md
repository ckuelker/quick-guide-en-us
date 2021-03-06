---
title: Zabbix
author: Christian Külker
date: 2020-12-27
version: 0.1.2
type: doc
disclaimer: True
TOC: True
categories:
- Monitoring
tags:
- Zabbix
- Nagios
- Icinga2
description: Installation of Zabbix on Debian 10 Buster

---

[Zabbix] is a free open source infrastructure monitoring software
with a PHP front end. Features are:

- monitoring of: networks, servers, applications, services, cloud resources,
  storage devices, databases, virtual machines, telephony, ...
- web-based monitoring and administration
- performance and security checks
- distributed monitoring, up to thousands of nodes
- autodiscovery
- charting, trending, SLA reporting
- sending alerts via e-mail, SMS and Jabber
- data stored in a relational database

[Zabbix] ships with an API for developers that provides access to almost all
functions in [Zabbix]. It supports a two-way integration with most other
software. The API can be used to integrate [Zabbix] functions into third-party
software.

## Debian Packages

### Zabbix client

- `zabbix-agent`
- `zabbix-cli`
- `zabbix-frontend-php`

### Zabbix server

- `zabbix-server-mysql for MySQL/MariaDB`
- `zabbix-server-pgsql for PostgreSQL`

### Proxy and gateways

- `zabbix-proxy-mysql`
- `zabbix-proxy-pgsql`
- `zabbix-proxy-sqlite3`
- `zabbix-java-gateway`

## Installing Zabbix on Debian 10 Buster

The following instructions will try to be as generic as possible. This guide
will show how to install and configure the latest release (4.0.4) of [Zabbix]
Server on Debian 10 with a MySQL (MariaDB) database, PHP for the front end and
Apache Web Server 2 as the web interface. This is not the latest release that
would run on Debian 10 Buster. Since this is the official version for Debian 10
Buster, updates will be available. However it is possible to install newer
versions on Debian 10 Buster from the [Zabbix] web site.

### Zabbix 4.0.4 Server

Install the [Zabbix] server with MySQL (MariaDB) support.

```shell
aptitude install zabbix-server-mysql
```

Secure the database server (if not already done). The questions are
self-explanatory.

```shell
mysql_secure_installation
```

Create the database

```shell
mysql --defaults-extra-file=/etc/mysql/debian.cnf
CREATE DATABASE zabbix CHARACTER SET utf8 COLLATE utf8_bin;
CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'CHANGE_ME';
GRANT ALL PRIVILEGES ON `zabbix`.* TO 'zabbix'@'localhost';
FLUSH PRIVILEGES;
quit
```

Unfortunately the table definition of `host_inventory` has to be changed for
the table to fit into the mariadb 10.3 dynamic table format.  (see
[row-size-too-large] for details)

Add the following two lines at the beginning of:
`/usr/share/zabbix-server-mysql/schema.sql.gz`

```
SET GLOBAL innodb_default_row_format='dynamic';
SET SESSION innodb_strict_mode=ON;
```

And at about line 1280 change all `varchar()` definition within the table
`host_inventory` with numbers smaller than 256 to 256 like so `varchar(256)`.


Populate the database

```shell
cd /usr/share/zabbix-server-mysql
zcat schema.sql images.sql data.sql|mysql zabbix --user=zabbix \
--password=CHANGE_ME
```

Add user database and password to `/etc/zabbix/zabbix_server.conf` and change
user and permission of this file.

```shell
chown zabbix /etc/zabbix/zabbix_server.conf
chnod 640 /etc/zabbix/zabbix_server.conf
```

```shell
echo "DBName=zabbix" >> /etc/zabbix/zabbix_server.conf
echo "DBUser=zabbix" >> /etc/zabbix/zabbix_server.conf
echo "DBPassword=CHANGE_ME" >> /etc/zabbix/zabbix_server.conf
```

In case you use a firewall you have to open port `tcp/10051`. There is also the
service `zabbix-server` for `firewalld`.

### Zabbix 4.0.4 Front End

### Timezone

Remember to specify a PHP date.timezone in
`/etc/apache2/conf-available/zabbix-frontend-php.conf`.

#### Securing Zabbix 4.0.4 PHP Front End

To restrict the directories allowed to be read by your [Zabbix] PHP front end,
setting a PHP open_basedir directive is suggested.

Put this example in your
`/etc/apache2/conf-available/zabbix-frontend-php.conf`, near the other
php_values: (in **one** line, without the backslash)

```
php_admin_value open_basedir /usr/share/zabbix/:/var/lib/zabbix:/etc/zabbix:\
/usr/share/javascript:/usr/share/fonts
```

### Setting Better Default Passwords

A one line command to set the `Admin` password.

```shell
PW=`/usr/bin/pwgen -y -N 1 $((8 + RANDOM % 12 ))|/usr/bin/perl -pe 'chomp'`;\
echo "$PW";\
echo "update users set passwd=md5('$PW') where alias='Admin';"|mysql zabbix
```

A one line command to set the `guest` password.

```shell
PW=`/usr/bin/pwgen -y -N 1 $((8 + RANDOM % 12 ))|/usr/bin/perl -pe 'chomp'`;\
echo "$PW";\
echo "update users set passwd=md5('$PW') where alias='guest';"|mysql zabbix
```

## Zabbix 4.0.4 Client

The client in the [Zabbix] world is called `agent`. Installation is straight
forward.

```shell
aptitude install zabbix-agent
```

Under Debian 10 Buster is the [Zabbix] client configured to use a [Zabbix]
server on `localhost`. Of course this is seldom the case. To use a different
server, add server IP to the entries of `Server` and `ServerActive` parameters
inside the client configuration `/etc/zabbix/zabbix_agentd.conf`.

In case you use a firewall you have to open port `tcp/10050`. There is also the
service `zabbix-agent` for `firewalld`.

## Links

- [zabbix]
- [documentation]
- [row-size-too-large]

## Critique Zabbix 4.0.4

While the installation of [Zabbix] is easy and straight forward and a lot of
data can be collected easily with templates and nice graphs can be made
visible, [Zabbix] 4.0.4 (and likely others) suffers from architecture
deficits, probably resulting from the very flexible data model.

- Automatic setup of Zabbix server templates differs from other servers
- Host groups do not allow to apply automatic template assignments
- Template assignments have to be done on a per host basis
- Not working/supported templates distributed (MySQL)
- Not working/supported templates can be chosen
- When chosen a not supported template [Zabbix] will not complain
- Working data queries are not always displayed, but only problems: seeing no
  problems, do not mean that there is no problem, as some [Zabbix] problems are
  discarded silently: this is a no go for a monitoring system (this forbids to
  use [Zabbix] as the only monitoring system for an installation)
- No easy default graph setup. All graphs have to be chosen for each host
- No default dashboard/ overview over working services/data collection
- Encryption not default setup
- Discovered hosts can not be added via web interface
- Maps are not updated automatically (and not added/updated from database)
- Difficult to add templates/ data queries (for inexperienced people)
- Number of unsupported items are displayed in dashboard, but no link and
  difficult to understand which item is not supported
- When adding a template to a host, not a button like "update" (as displayed
  under the form) but the link "add" inside the form has to be clicked: this
  breaks usability and is not intuitive. A button would be preferable.
- Naming of boxes are not intuitive: box 1 "Linked templates" and box 2 "Link
  new template". Changing box 2 will update box 1. A different design with only
  1 box would be better. Also the "add" link works as a **button** that do not
  add something, but link a template. How confusing can it be?
- Some hosts added to host groups are shown in the inventory, but others are
  not. This points to the fact that some concepts are not understandable via
  the web interface and that some data dependency concepts are implicit and can
  only be understood via a manual
- Most functions of the web interface have no default query, but need a manual
  query. While this clearly has the advantage of flexibility, junior admins
  would probably do not know what to query in the first place.
- Default query of Audit Report says 'no data'
- Default query of Action log Report says 'no data'
- No host specific reports
- When clicking a top level tab like "Monitoring" the sub level tab is updated,
  but not the page content and a second click on the sub level tab has to be
  performed. This is clearly a bug
- ...

Therefore one get the impression that the web interface in its fresh installed
form is not programmed with the vision in mind to monitor computer systems but
to query a database in as many as possible ways.

However, there are good preconfigured [Zabbix] instances that have a plethora
of graphs, dash boards and other aggregated data points from which a system
administrator can understand the situation (an not only the problems) of an
installation/cluster. As a consequence, there is a huge added value, for a
preconfigured [Zabbix] installation as some cluster administration stacks
provide.

A word on [Zabbix] deployment. In case a preconfigured [Zabbix] server needs to
be deployed to many sites or if it has to be set up for a certain hardware
entity, like a rack or chassis (as a cluster entity for example), the [Zabbix]
server configuration lies mostly in its database and not in from of
configuration files. This means [Zabbix] has no separation of configuration and
process data. Therefore, if the configuration was only performed via the web
interface, the content of the database has to be duplicated and host
values/process data would need to be removed from the database for new hardware
entities. This also would make [Zabbix] updates very difficult. The solution or
an easy solution to this problem is not known to me, but seems like a major
problem. Other monitor systems like [Icinga2] or [Nagios] which are configured
via files can easily be distributed/cloned via git, puppet or other means, as
they separate process data from configuration data.

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2020-12-27 | Critique Zabbix 4.0.4                                |
| 0.1.1   | 2020-12-18 | Add hint for client configuration                    |
| 0.1.0   | 2020-12-15 | Initial release                                      |


[zabbix]: https://www.zabbix.com/
[documentation]:  https://www.zabbix.com/documentation/current/manual
[row-size-too-large]: https://mariadb.com/kb/en/troubleshooting-row-size-too-large-errors-with-innodb/
[icinga2]: https://icinga.com/
[nagios]: https://www.nagios.org/
