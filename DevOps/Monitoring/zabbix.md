---
title: Zabbix
author: Christian Külker
date: 2023-05-26
version: 0.1.4
type: doc
disclaimer: True
toc: True
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

[Zabbix] ships with a developer API that provides access to almost all of the
functionality in [Zabbix]. It supports two-way integration with most other
software. The API can be used to integrate [Zabbix] functions into third party
software.

## Debian Packages

### Zabbix client

A `zabbix` client is called __agent__ in `zabbix` lingo.

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

The following instructions will try to be as general as possible. This guide
will show you how to install and configure the latest release (4.0.4) of the
[Zabbix] server on Debian 10 with a MySQL (MariaDB) database, PHP for the
frontend and Apache Web Server 2 for the web interface. This is not the latest
release that would run on Debian 10 Buster. Since this is the official release
for Debian 10 Buster, updates will be available. However, it is possible to
install newer versions on Debian 10 Buster from the [Zabbix] website.

### Zabbix 4.0.4 Server

Install the [Zabbix] server with MySQL (MariaDB) support.

```bash
aptitude install zabbix-server-mysql
```

Back up the database server (if not already done). These questions are
self-explanatory.

```bash
mysql_secure_installation
```

Create the database

```bash
mysql --defaults-extra-file=/etc/mysql/debian.cnf
CREATE DATABASE zabbix CHARACTER SET utf8 COLLATE utf8_bin;
CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'CHANGE_ME';
GRANT ALL PRIVILEGES ON `zabbix`.* TO 'zabbix'@'localhost';
FLUSH PRIVILEGES;
quit
```

Unfortunately, the table definition of `host_inventory` needs to be changed to
fit the mariadb 10.3 dynamic table format.  (see [row-size-too-large] for
details).

Add the following two lines at the beginning of:
`/usr/share/zabbix-server-mysql/schema.sql.gz`

```sql
SET GLOBAL innodb_default_row_format='dynamic';
SET SESSION innodb_strict_mode=ON;
```

And at about line 1280, change all `varchar()` definitions within the
`host_inventory` table with numbers less than 256 to 256 like this
`varchar(256)`.


Populate the database

```bash
cd /usr/share/zabbix-server-mysql
zcat schema.sql images.sql data.sql|mysql zabbix --user=zabbix \
--password=CHANGE_ME
```

Add user database and password to `/etc/zabbix/zabbix_server.conf` and change
user and permission of this file.

```bash
chown zabbix /etc/zabbix/zabbix_server.conf
chnod 640 /etc/zabbix/zabbix_server.conf
```

```bash
echo "DBName=zabbix" >> /etc/zabbix/zabbix_server.conf
echo "DBUser=zabbix" >> /etc/zabbix/zabbix_server.conf
echo "DBPassword=CHANGE_ME" >> /etc/zabbix/zabbix_server.conf
```

If you are using a firewall, you will need to open port `tcp/10051`. There is
also the service `zabbix-server` for `firewalld`.

### Zabbix 4.0.4 Front End

### Timezone

Remember to specify a PHP date.timezone in
`/etc/apache2/conf-available/zabbix-frontend-php.conf`.

#### Securing Zabbix 4.0.4 PHP Front End

To restrict the directories that are allowed to be read by your [Zabbix] PHP
frontend, setting a PHP `open_basedir` directive is suggested.

Put this example in your
`/etc/apache2/conf-available/zabbix-frontend-php.conf`, near the other
php_values: (in **one** line, without the backslash)

```php
php_admin_value open_basedir /usr/share/zabbix/:/var/lib/zabbix:/etc/zabbix:\
/usr/share/javascript:/usr/share/fonts
```

### Setting Better Default Passwords

A one-line command to set the `admin` password.

```bash
PW=`/usr/bin/pwgen -y -N 1 $((8 + RANDOM % 12 ))|/usr/bin/perl -pe 'chomp'`;\
echo "$PW";\
echo "update users set passwd=md5('$PW') where alias='Admin';"|mysql zabbix
```

A one-line command to set the `guest` password.

```bash
PW=`/usr/bin/pwgen -y -N 1 $((8 + RANDOM % 12 ))|/usr/bin/perl -pe 'chomp'`;\
echo "$PW";\
echo "update users set passwd=md5('$PW') where alias='guest';"|mysql zabbix
```

## Zabbix 4.0.4 Client

The client in the [Zabbix] world is called an `agent`. Installation is
straightforward.

```bash
aptitude install zabbix-agent
```

On Debian 10 Buster, the [Zabbix] client is configured to use a [Zabbix] server
on `localhost`. Of course, this is rarely the case. To use a different server,
add the server IP to the entries of the `Server` and `ServerActive` parameters
in the client configuration `/etc/zabbix/zabbix_agentd.conf`.

If you are using a firewall, you will need to open port `tcp/10050`. There is
also the service `zabbix-agent` for `firewalld`.

There are several ways to add a client to the [Zabbix] server. One method is to
log into the web frontend, select 'Configuration', 'Hosts' and press the blue
'Create Host' button in the top right corner. Add a template via
'Configuration' -> 'Host' -> click on the host name in the name column ->
'Templates' (of this host).

## Links

- [zabbix]
- [documentation]
- [row-size-too-large]

## Critique Zabbix 4.0.4

While the installation of [Zabbix] is simple and straightforward, and a lot of
data can be easily collected with templates and nice graphs can be made
visible, [Zabbix] 4.0.4 (and probably others) suffers from architectural
shortcomings, probably resulting from the very flexible data model.

- Automatic setup of Zabbix server templates differs from other servers.
- Host groups don't allow automatic template assignments to be applied.
- Template assignments must be done on a per-host basis.
- Unsupported or non-working templates are distributed (MySQL).
- Unsupported or non-working templates can be selected.
- When an unsupported template is chosen, Zabbix doesn't issue a complaint.
- Working data queries aren't always displayed, only problems are. Not seeing
  problems doesn't mean there aren't any, as some Zabbix issues are silently
  ignored. This is unacceptable for a monitoring system, making Zabbix
  unsuitable as the sole monitoring system for an installation.
- There's no simple default graph setup. All graphs must be chosen for each host.
- There's no default dashboard or overview of working services/data collection.
- Encryption isn't set up by default.
- Discovered hosts can't be added via the web interface.
- Maps aren't updated automatically and aren't added/updated from the database.
- It's difficult to add templates or data queries (especially for inexperienced
   people).
- The number of unsupported items is displayed on the dashboard, but there's no
  link and it's hard to understand which item is unsupported.
- When adding a template to a host, the "add" link inside the form should be
  clicked, not an "update" button (as displayed under the form). This disrupts
  usability and isn't intuitive. A button would be more preferable.
- The naming of boxes isn't intuitive: Box 1 is "Linked templates" and Box 2 is
  "Link new template". Changing Box 2 will update Box 1. A different design
  with only one box would be better. Additionally, the "add" link acts as a
  __button__ that doesn't add something, but links a template. This can be 
  confusing.
- Some hosts added to host groups are shown in the inventory, but others
  aren't. This suggests that some concepts can't be understood via the web
  interface and that some data dependency concepts are implicit and can only be
  understood through a manual.
- Most functions of the web interface don't have a default query, but require a
  manual query. While this provides flexibility, junior admins may not know
  what to query initially.
- The default query for the Audit Report indicates 'no data'.
- The default query for the Action Log Report indicates 'no data'.
- There are no host-specific reports.
- When clicking a top-level tab like "Monitoring", the sub-level tab updates,
  but not the page content. A second click on the sub-level tab must be
  performed. This is clearly a bug.
- ...

Therefore, one gets the impression that the web interface in its freshly
installed form is not programmed with the vision of monitoring computer
systems, but of querying a database in as many ways as possible.

However, there are good preconfigured [Zabbix] instances that have a plethora
of graphs, dashboards and other aggregated data points from which a system
administrator can understand the situation (and not just the problems) of an
installation/cluster. As a result, there is a huge added value to a
pre-configured [Zabbix] installation, such as some cluster management stacks
provide.

A word about [Zabbix] deployment. If a pre-configured [Zabbix] server needs to
be deployed to many sites, or if it needs to be set up for a specific hardware
entity such as a rack or chassis (e.g. as a cluster entity), the [Zabbix]
server configuration is mostly stored in its database and not in a set of
configuration files. This means that [Zabbix] has no separation between
configuration and process data. Therefore, if the configuration is done only
through the web interface, the database content would have to be duplicated and
host values/process data would have to be removed from the database for new
hardware entities. This would also make [Zabbix] updates very difficult. I am
not aware of a solution or a simple workaround for this problem, but it seems
to be a major issue. Other monitoring systems like [Icinga2] or [Nagios] which
are configured via files can be easily distributed/cloned via git, puppet or
other means as they separate process data from configuration data.

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.4   | 2023-05-26 | Improve writing                                      |
| 0.1.3   | 2022-06-07 | Client explanation, shell->bash                      |
| 0.1.2   | 2020-12-27 | Critique Zabbix 4.0.4                                |
| 0.1.1   | 2020-12-18 | Add hint for client configuration                    |
| 0.1.0   | 2020-12-15 | Initial release                                      |


[zabbix]: https://www.zabbix.com/
[documentation]:  https://www.zabbix.com/documentation/current/manual
[row-size-too-large]: https://mariadb.com/kb/en/troubleshooting-row-size-too-large-errors-with-innodb/
[icinga2]: https://icinga.com/
[nagios]: https://www.nagios.org/

