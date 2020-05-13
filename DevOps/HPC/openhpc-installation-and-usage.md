---
title: Openhpc Installation And Usage
linkTitle: OpenHPC
author: Christian Külker
date: 2017-05-19
version: 0.1.0
type: doc
disclaimer: True
TOC: True
categories:
- HPC
tags:
- OpenHPC Installation
- OpenHPC Usage
- Warewulf
- CentOS 7
- Ganglia
- selinux
- Slurm
description: OpenHPC Installation And Usage

---

OpenHPC (ohpc) is made of different components. One component is the
cluster provisioning by Warewulf.

## Installation of OpenHPC

### [SERVER] Prerequisites Before Installing OpenHPC

Install CentOS 7.2.511

### Download Install The Guide For OpenHPC

For this document `rev 3fc5abc` was  used.

### [SERVER] Disable `selinux`

Check if `selinux` is enabled:

```shell
if selinuxenables; then echo yes; else echo no; fi
```

This will print if `selinux` is enabled at boot time. Temporarily disable
`selinux`.

```shell
setenforce 0
```

Check with

```shell
getenforce
```

Should print

```shell
Permissive
```

### [SERVER] Kick Start OpenHPC Installation

```shell
yum install epel-release
rpm -ivh http://build.openhpc.community/OpenHPC:/1.1/CentOS_7.2/x86_64/ohpc-release-1.1-1.x86_64.rpm

systemctl disable firewalld
systemctl stop firewalld

yum -y groupinstall ohpc-base
```

### [SERVER] Install Convenient Tools

```shell
yum install tree zsh rsync wget mlocate vim
```

### [SERVER] Install Dependencies

Not mentioned dependencies:

```
ifconfig
```

```shell
yum install net-tools
```

### [SERVER] Install Warewulf Cluster For OpenHPC

```shell
yum -y groupinstall ohpc-warewulf
```

### [SERVER] Setup The Correct Time On The Front End Node

```shell
systemctl enable ntpd.service
systemctl restart ntpd
```

### [SERVER] Install Workload Manager Slurm (Server)

```shell
yum -y groupinstall ohpc-slurm-server
useradd slurm
```

### [SERVER] OpenHPC Environment

Use existing environment file or create your own. Make sure data
for your site is correct.

```shell
source /opt/kuelker/env/ohpc ADAMSPASS

env|grep OHPC|sort
OHPC_CLIENT_ARCH=ADAMSPASS
OHPC_CLIENT_INTERNAL_INTERFACE=eno1
OHPC_CLIENT_INTERNAL_NETMASK=255.255.0.0
OHPC_CLIENT_PROVISION_INTERFACE=eno1
OHPC_SERVER_CHROOT=/opt/ohpc/admin/images/i86_x64/centos7.2
OHPC_SERVER_INTERNAL_INTERFACE=enp2s0
OHPC_SERVER_INTERNAL_NETMASK=255.255.0.0
OHPC_SERVER_IP=172.16.254.254
OHPC_SERVER_NAME=gw200
```

During this guide it is assumed that the environment is sourced in before at
the beginning. That is especially true if variables are in the commands.
Variables always start with ``$OHPC_`` or ``${OHPC_``.

```shell
source /opt/kuelker/env/ohpc ADAMSPASS
```

### [SERVER] Complete Basic OpenHPC/ Warewulf Setup

Source in the correct environment (see previous chapter).

```shell
source /opt/kuelker/env/ohpc ADAMSPASS
```

```shell
# Configure Warewulf provisioning to use desired internal interface
export PFN=/etc/warewulf/provision.conf
perl -pi -e "s/device = eth1/device = ${OHPC_SERVER_INTERNAL_INTERFACE}/" $PFN
# Enable tftp service for compute node image distribution
perl -pi -e "s/^\s+disable\s+= yes/ disable = no/" /etc/xinetd.d/tftp
# Enable http access for Warewulf cgi-bin directory to support newer apache syntax
export MFN=/etc/httpd/conf.d/warewulf-httpd.conf
perl -pi -e "s/cgi-bin>\$/cgi-bin>\n Require all granted/" $MFN
perl -pi -e "s/Allow from all/Require all granted/" $MFN
perl -ni -e "print unless /^\s+Order allow,deny/" $MFN
# Enable internal interface for provisioning
ifconfig ${OHPC_SERVER_INTERNAL_INTERFACE}\
         ${OHPC_SERVER_IP}\
         netmask ${OHPC_SERVER_INTERNAL_NETMASK} up
```

```shell
# Restart/enable relevant services to support provisioning
systemctl restart xinetd
systemctl enable mariadb.service
systemctl restart mariadb
systemctl enable httpd.service
systemctl restart httpd
```

### [SERVER] Configure NFS /home

Later a NFS server is needed

```shell
source /opt/kuelker/env/ohpc ADAMSPASS
yum install nfs-utils
mkdir -p /opt/ohpc/home
echo "${OHPC_HOME_BASE} *(${OHPC_SERVER_NFSEXP10})" >> /etc/exports
echo "/opt/ohpc/pub     *(${OHPC_SERVER_NFSEXP11})" >> /etc/exports
exportfs -a
systemctl restart nfs
systemctl enable nfs-server
```

### [SERVER] Install Ganglia to Front Node

Make sure `selinux` is disabled! Installation works with `seleinux`, but if
`selinux` is enabled ganglia can not collect data. (There was an error
collecting ganglia data (127.0.0.1:8652): fsockopen error: Permission denied)

```shell
setenforce 0

yum -y groupinstall ohpc-ganglia
```

Backup old configuration

```shell
cp /etc/ganglia/gmond.conf /etc/ganglia/gmond.conf.`date +'%F'`
cp /opt/ohpc/pub/examples/ganglia/gmond.conf /etc/ganglia
```

Source the environment

```shell
source /opt/kuelker/env/ohpc ADAMSPASS
```

Set the server name

```shell
perl -pi -e "s/<sms>/${OHPC_SERVER_NAME}/" /etc/ganglia/gmond.conf
```

Set the site name

```shell
cp /etc/ganglia/gmetad.conf /etc/ganglia/gmetad.conf.`date +'%F'`
echo "gridname $OHPC_SITE_NAME" >> /etc/ganglia/gmetad.conf
```

Enable and start Ganglia

```shell
systemctl enable gmond
systemctl enable gmetad
systemctl start gmond
systemctl start gmetad

systemctl try-restart httpd
```

### Install Ganglia on the Clients

```shell
source /opt/kuelker/env/ohpc ADAMSPASS
yum -y --installroot=$OHPC_SERVER_CHROOT_TEST install ganglia-gmond-ohpc
chroot $OHPC_SERVER_CHROOT_TEST systemctl enable gmond
```

## Documentation

The official install guide:

* [guide download http](http://www.openhpc.community/downloads/)
* [guide download ftp](ftp://ftp.vcu.edu/pub/linux/openhpc/)

