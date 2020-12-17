---
title: Zabbix
author: Christian Külker
date: 2020-12-15
version: 0.1.0
type: doc
disclaimer: True
TOC: True
categories:
- Monitoring
tags:
- Zabbix
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

In case you use a firewall you have to open port `tcp/10050`. There is also the
service `zabbix-agent` for `firewalld`.

## Links

- [zabbix]
- [documentation]
- [row-size-too-large]

[zabbix]: https://www.zabbix.com/
[documentation]:  https://www.zabbix.com/documentation/current/manual
[row-size-too-large]: https://mariadb.com/kb/en/troubleshooting-row-size-too-large-errors-with-innodb/
