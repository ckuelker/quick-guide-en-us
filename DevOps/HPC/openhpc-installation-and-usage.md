---
title: OpenHPC Installation And Usage
linkTitle: OpenHPC
author: Christian Külker
date: 2024-07-25
version: 0.1.3
type: doc
disclaimer: True
toc: True
categories:
- HPC
commands:
- selinux
- selinuxenables
- setenforce
- getenforce
- yum
- systemctl
- rpm
- ifconfig
- useradd
- perl
- exportfs
- chroot
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
cluster provisioning by [Warewulf].

The version of [Warewulf] that comes with [OpenHPC] is 3.6 which is the latest
version from 2014.05.28 as of 2017-05-26 (time of first writing). As of
2021-01-02 the [Warewulf] version is outdated, but at least to [Warewulf] 3.9
(2019,2020) the concepts are very similar and this document might help. Some
sections contain '#' as a marker for actions executed as root. Unlike other
documents this was added to better differentiate between actions executed
inside the [Warewulf] shell and actions executed as root.

```bash
ls -d /opt/ohpc/pub/doc/contrib/warewulf*
/opt/ohpc/pub/doc/contrib/warewulf-cluster-ohpc-3.6
/opt/ohpc/pub/doc/contrib/warewulf-common-ohpc-3.6
/opt/ohpc/pub/doc/contrib/warewulf-provision-ohpc-3.6
/opt/ohpc/pub/doc/contrib/warewulf-vnfs-ohpc-3.6
rpm -qif /usr/share/warewulf/pxelinux.0|grep Version
Version : 3.6
```

## Installation of OpenHPC

### Prerequisites Before Installing OpenHPC

Install CentOS 7.2.511

### Download Install The Guide For OpenHPC

For this document `rev 3fc5abc` was  used.

### Disable Selinux

Check if `selinux` is enabled:

```bash
if selinuxenables; then echo yes; else echo no; fi
```

This will print if `selinux` is enabled at boot time. Temporarily disable
`selinux`.

```bash
setenforce 0
```

Check with

```bash
getenforce
```

Should print

```bash
Permissive
```

### Kick Start OpenHPC Installation

```bash
yum install epel-release
rpm -ivh http://build.openhpc.community/OpenHPC:/1.1/CentOS_7.2/x86_64/\
ohpc-release-1.1-1.x86_64.rpm

systemctl disable firewalld
systemctl stop firewalld

yum -y groupinstall ohpc-base
```

### Install Convenient Tools

```bash
yum install tree zsh rsync wget mlocate vim
```

### Install Dependencies

Not mentioned dependencies:

```
ifconfig
```

```bash
yum install net-tools
```

### Install Warewulf Cluster For OpenHPC

```bash
yum -y groupinstall ohpc-warewulf
```

### Setup Correct Time On The Front End Node

```bash
systemctl enable ntpd.service
systemctl restart ntpd
```

### Install Workload Manager Slurm (Server)

```bash
yum -y groupinstall ohpc-slurm-server
useradd slurm
```

### OpenHPC Environment

Use existing environment file or create your own. Make sure data
for your site is correct.

```bash
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

```bash
source /opt/kuelker/env/ohpc ADAMSPASS
```

### Complete Basic OpenHPC/ Warewulf Setup

Source in the correct environment (see previous chapter).

```bash
source /opt/kuelker/env/ohpc ADAMSPASS
```

 Configure [Warewulf] provisioning to use desired internal interface

```bash
export PFN=/etc/warewulf/provision.conf
perl -pi -e "s/device = eth1/device = ${OHPC_SERVER_INTERNAL_INTERFACE}/" $PFN
```
Enable tftp service for compute node image distribution

```bash
perl -pi -e "s/^\s+disable\s+= yes/ disable = no/" /etc/xinetd.d/tftp
```

Enable http access for [Warewulf] cgi-bin directory to support newer apache
syntax

```bash
export MFN=/etc/httpd/conf.d/warewulf-httpd.conf
perl -pi -e "s/cgi-bin>\$/cgi-bin>\n Require all granted/" $MFN
perl -pi -e "s/Allow from all/Require all granted/" $MFN
perl -ni -e "print unless /^\s+Order allow,deny/" $MFN
```

Enable internal interface for provisioning

```bash
ifconfig ${OHPC_SERVER_INTERNAL_INTERFACE}\
         ${OHPC_SERVER_IP}\
         netmask ${OHPC_SERVER_INTERNAL_NETMASK} up
```
Restart/enable relevant services to support provisioning

```bash
systemctl restart xinetd
systemctl enable mariadb.service
systemctl restart mariadb
systemctl enable httpd.service
systemctl restart httpd
```

### Configure NFS /home

Later a NFS server is needed

```bash
source /opt/kuelker/env/ohpc ADAMSPASS
yum install nfs-utils
mkdir -p /opt/ohpc/home
echo "${OHPC_HOME_BASE} *(${OHPC_SERVER_NFSEXP10})" >> /etc/exports
echo "/opt/ohpc/pub     *(${OHPC_SERVER_NFSEXP11})" >> /etc/exports
exportfs -a
systemctl restart nfs
systemctl enable nfs-server
```

### Install Ganglia to Front Node

Make sure `selinux` is disabled! Installation works with `seleinux`, but if
`selinux` is enabled ganglia can not collect data. (There was an error
collecting ganglia data (127.0.0.1:8652): `fsockopen error: Permission denied`)

```bash
setenforce 0

yum -y groupinstall ohpc-ganglia
```

Backup old configuration

```bash
cp /etc/ganglia/gmond.conf /etc/ganglia/gmond.conf.`date +'%F'`
cp /opt/ohpc/pub/examples/ganglia/gmond.conf /etc/ganglia
```

Source the environment

```bash
source /opt/kuelker/env/ohpc ADAMSPASS
```

Set the server name

```bash
perl -pi -e "s/<sms>/${OHPC_SERVER_NAME}/" /etc/ganglia/gmond.conf
```

Set the site name

```bash
cp /etc/ganglia/gmetad.conf /etc/ganglia/gmetad.conf.`date +'%F'`
echo "gridname $OHPC_SITE_NAME" >> /etc/ganglia/gmetad.conf
```

Enable and start Ganglia

```bash
systemctl enable gmond
systemctl enable gmetad
systemctl start gmond
systemctl start gmetad

systemctl try-restart httpd
```

### Install Ganglia on the Clients

```bash
source /opt/kuelker/env/ohpc ADAMSPASS
yum -y --installroot=$OHPC_SERVER_CHROOT_TEST install ganglia-gmond-ohpc
chroot $OHPC_SERVER_CHROOT_TEST systemctl enable gmond
```

## Usage of OpenHPC and Warewulf

### Using Warewulf VNFS

#### Creating A Base VNFS Template (chroot) For The Backend Nodes

```bash
source /opt/kuelker/env/ohpc ADAMSPASS
mkdir -p $OHPC_SERVER_CHROOT
wwmkchroot centos-7.2 $OHPC_SERVER_CHROOT
```

You can see if it is create with:

```bash
wwsh vnfs list
```

### Update Base VNFS Template With DNS, Slurm, NTP, Kernel Modules and Utilities

This is executed on the server, but it effects the chroot (OHPC_SERVER_CHROOT).

```bash
source /opt/kuelker/env/ohpc ADAMSPASS
cp -p /etc/resolv.conf ${OHPC_SERVER_CHROOT}/etc/resolv.conf
```

Add Slurm client support
```bash
yum -y --installroot=${OHPC_SERVER_CHROOT} groupinstall ohpc-slurm-client
````

Add Network Time Protocol (NTP) support

```bash
yum -y --installroot=${OHPC_SERVER_CHROOT} install ntp
```

Add kernel drivers

```bash
yum -y --installroot=${OHPC_SERVER_CHROOT} install kernel
```

Include modules user environment

```bash
yum -y --installroot=${OHPC_SERVER_CHROOT} install lmod-ohpc
```

Include Utilities

```bash
yum -y --installroot=${OHPC_SERVER_CHROOT} install tree vim zsh wget rsync\
screen
```

Configure NTP server

```bash
echo "server ${sms_ip}" >> ${OHPC_SERVER_CHROOT}/etc/ntp.conf
```

### Enable NTP Server in CHROOT

Open the chroot

```bash
source /opt/kuelker/env/ohpc ADAMSPASS
chroot ${OHPC_SERVER_CHROOT} systemctl enable ntpd
```

### Add DNS and SSH Configuration To The VNFS Template

Inside chroot

```bash
cp /etc/resolv.conf ${OHPC_SERVER_CHROOT}/etc
wwinit ssh_key
cat ~/.ssh/cluster.pub >> ${OHPC_SERVER_CHROOT}/root/.ssh/authorized_keys
echo "$OHPC_SERVER_IP:$OHPC_HOME_BASE $OHPC_HOME_BASE nfs\
$OHPC_CLIENT_NFSMNT11 0 0" >> ${OHPC_SERVER_CHROOT}/etc/fstab
echo "$OHPC_SERVER_IP:/opt/ohpc/pub /opt/ohpc/pub nfs\
$OHPC_CLIENT_NFSMNT10 0 0" >> ${OHPC_SERVER_CHROOT}/etc/fstab
export CFG=/etc/slurm/slurm.conf
perl -pi -e "s/ControlMachine=\S+/ControlMachine=${sms_name}/" $CFG
```

Adjust slurm configuration to match your hardware: Sockets, CoresPerSocket, and
ThreadsPerCore.

```bash
vim /etc/slurm/slurm.conf
```

### Re-create/ Re-Update VNFS Image from Template

This is needed if the content of the chroot has changed.

```bash
source /opt/kuelker/env/ohpc ADAMSPASS
```

Assemble the **VNFS** On The Frontend Node by wwvnfs .

```bash
wwvnfs --chroot $OHPC_SERVER_CHROOT
```

This will print some output

```
Using ’centos7.2’ as the VNFS name
Creating VNFS image from centos7.2
Building new chroot...
Building and compressing the final image
Cleaning temporary files
VNFS ’centos7.2’ has been imported
Done.
Wrote a new configuration file at: /etc/warewulf/vnfs/centos7.2.conf
```

Automated for update:

```bash
wwvnfs -y --chroot $OHPC_SERVER_CHROOT
```

After a **VNFS** Images has been created from chroot also the name can be used
to update it:

```bash
wwvnfs -y centos7.2
```

### Build a new VNFS (chroot) For Testing

```bash
wwvnfs centos7.2-testing -c /opt/ohpc/admin/images/i86_x64/centos7.2-testing
```

## Warewulf Shell wwsh

### Enable Tab Completion

```bash
yum install perl-Term-ReadLine-Gnu
```

### Creating a New Warewulf Database

The first invocation of `wwsh` will create the database, if not already done.

```bash
wwsh
```

Exit the shell with exit or quit.

## Boostrap

Create The Bootstrap Image from the Master Server

```bash
wwbootstrap ‘uname -r‘
```

This creates output

```
Number of drivers included in bootstrap: 461
Number of firmware images included in bootstrap: 93
Building and compressing bootstrap
Integrating the Warewulf bootstrap: 3.10.0-327.36.2.el7.x86_64
Including capability: provision-adhoc
Including capability: provision-files
Including capability: provision-selinux
Including capability: provision-vnfs
Including capability: setup-filesystems
Including capability: transport-http
Compressing the initramfs
Locating the kernel object
Bootstrap image ’3.10.0-327.36.2.el7.x86_64’ is ready
Done.
```

To understand if the bootstrap image has bee created the maria/mysql database
should be filled:

```sql
use warewulf;select * from lookup;
Database changed
+----+-----------+-------+----------------------------+
| id | object_id | field | value                      |
+----+-----------+-------+----------------------------+
|  4 |         1 | NAME  | 3.10.0-327.36.2.el7.x86_64 |
|  3 |         1 | _ID   | 1                          |
+----+-----------+-------+----------------------------+
2 rows in set (0.00 sec)
```

You can also use the `wwsh` command to query the database

```bash
wwsh
bootstrap list
BOOTSTRAP NAME             SIZE (M)
3.10.0-327.36.2.el7.x86_64 26.4
```

### Re-Create/ Re-Updating bootstrap

```bash
cd /opt/ohpc/admin/images/i86_x64/centos7.2
```

Change content

```bash
wwsh -y bootstrap rebuild
```

Update **VNFS**

```bash
wwvnfs -y centos7.2
```

### Creating a Bootstrap from CHROOT


```bash
wwbootstrap --chroot $OPT_SERVER_CHROOT --name \
3.10.0-327.36.3.el7.x86_64 3.10.0-327.36.3.el7.x86_64
```

This has a BUG:

```
Insecure dependency in system while running with -T switch at
usr/bin/wwbootstratep is to add VNFS via provision line 309
```

Resolution: do not use –name

## NETDEV

### Adding General Configuration For /etc/sysconfig/network

In our setip the `${sms_ip}` is also the provisioning IP.

```bash
echo "GATEWAYDEV=${sms_ip}" > /tmp/network.$$
wwsh -y file import /tmp/network.$$ --name network
wwsh -y file set network --path /etc/sysconfig/network --mode=0644 --uid=0
about to apply 3 action(s) to 1 file(s):

 SET: PATH                  = /etc/sysconfig/network
 SET: MODE                  = 0644
 SET: UID                   = 0

Proceed?
```

This action was producing an entry `/tmp/network.$$` in the database, that was
overwritten with `/etc/sysconfig/network` and the content of
`GATEWAYDEV=${OHPC_SERVER_IP}`.

## Nodes

Register (adding) nodes manually for provisioning or change the provisioning.

### Adding A Node Manually

#### Groveport

```bash
source /opt/kuelker/env/ohpc GROVEPORT
wwsh -y node new <NAME> --ipaddr=<IP> --hwaddr=<MAC> -D \
$OHPC_CLIENT_PROVISION_INTERFACE
```

**Example Groveport:**

```bash
wwsh -y node new n000 --ipaddr=172.16.0.249 --hwaddr=90:49:fa:01:3f:ce -D enp3s0
```

#### Adams Pass

```bash
source /opt/kuelker/env/ohpc ADAMSPASS
wwsh -y node new <NAME> --ipaddr=<IP> --hwaddr=<MAC> -D \
${OHPC_CLIENT_PROVISION_INTERFACE}
```

**Example Admas Pass:**

```bash
wwsh -y node new n001 --ipaddr=172.16.0.7 --hwaddr=00:1e:67:fa:18:13 -D eno1
```

Next step is to add **VNFS** via provision

## Nodes Provision

The entity provision in `wwsh`

```bash
$ wwsh provision list
NODE VNFS            BOOTSTRAP             FILES
===============================================================================
n000 centos7.2       3.10.0-327.36.2.el... dynamic_hosts,network
n001 centos7.2-te... 3.10.0-327.36.2.el... dynamic_hosts,network
```

## Node Stateful Provision (Deploy)

The default usage of [Warewulf] is to manage diskless nodes. This chapter
describes how to (1) install an OS to a node (2) boot that node to disk (3)
reinstall the node with the same OS. It does it for (A) CentOS and (B) Ubuntu.

In general the ichroot’s of [Warewulf] that are generated by the `wwmkchroot`
command cannot be used directly for stateful provisioning. It lacks a kernel
and grub installation. This can be solved by installing needed packages into
the chroot before bundling the vnfs.


### Bugs

So far there have been found 2 bugs: Postinstall makes a kernel crash and
re-installation do no work .

#### Post install shell do not work

The kernel crashes and not shell is invoked. It claims that cttyhack was not
found.  The problem is in `/srv/warwulf/initramfs/base`

```bash
grep -r cttyhack warewulf
Binary file warewulf/initramfs/base matches
```

Solution: (ONLY PARTLY WORKING - see below)

```bash
mkdir /tmp/CTTYHACK
cd /tmp/CTTYHACK
cpio -vid < /srv/warewulf/initramfs/base
grep -r cttyhack *
Binary file bin/busybox matches
etc/functions: setsid cttyhack sh
init: setsid cttyhack sh
init: setsid cttyhack sh
init: setsid cttyhack sh
```

Replace `with /bin/cttyhack /bin/sh` in init and `etc/functions`. Then pack the
archive again

```bash
cd /tmp/CTTYHACK
find .| cpio -ov > /srv/warewulf/initramfs/base
```

Then update the bootstrap image

```bash
wwbootstrap --chroot /opt/ohpc/admin/images/i86_x64/client02 3.13.0-24-generic
```

Remaining BUG: While the binary gets executed, the keyboard is dead.

#### MBR Not Cleared

Wiping the MBR do not work (reinstall do not work).

The [Warewulf] installer section mkbootable will only be executed if the MBR
was empty. (due to probably defect script) Reinstall is not possible, because
mkbootable is SKIPPED.

### Further Reading

- [Stateful Provisioning]

### Install an OS to a Node's Disk

#### CentOS 7.x

```bash
wwmkchroot centos-7 /opt/ohpc/admin/images/i86_x64/client01
yum --installroot=/var/chroots/client01 install kernel grub
```

#### Minimal Ubuntu 14.04 Trusty

Install dependencies:

```bash
yum install debootstrap.noarch dpkg
```

On CentOS it is not straight forward to create an Ubuntu chroot. First an
include file has to be created:

```bash
cd /usr/libexec/warewulf/wwmkchroot
cp include-deb include-ubuntu-chroot-on-centos
```

In this file one sanity check needs to be disabled. That check tries to verify
that `deboostrap` was installed as a **Debian** package. However **CentOS** is
an RPM based system and the `deboostrap` installed with RPM is just fine.

```
  # if ! dpkg -s debootstrap >/dev/null 2>&1; then
  #  echo "ERROR: Could not query dpkg for debootstrap"
  #  return 1
  # fi
```

Then a profile needs to be created that sources the new include file.

```bash
cd /usr/libexec/warewulf/wwmkchroot
vim ubuntu14.04-64-srv.tmpl
```

With the content:

```
  #DESC: A base 64 bit Ubuntu Trusty chroot
  # The general Deb include has all of the necessary functions, but requires
  # some basic variables specific to each chroot type to be defined.
  #. include-deb
  #. include-rhel
. include-ubuntu-chroot-on-centos

ARCH="amd64"
SUITE="trusty"
COMPONENTS="main,multiverse,restricted,universe"
DEB_MIRROR="http://mirror.pnl.gov/ubuntu"

  # Install additional packages with debootstrap
PKGLIST="openssh-server,openssh-client,isc-dhcp-client,pciutils,strace,\
nfs-common,nfs-kernel-server,ethtool,iproute,iputils-ping,iputils-arping,\
net-tools,linux-image-server,ubuntu-minimal"

vim:filetype=sh:syntax=sh:expandtab:ts=4:sw=4:
```

Due to the fact that the PATH for CentOS and Ubuntu is different this has to be
adjusted before the chroot can be made. Otherwise deboostrap will fail to mount
proc , because the mount command of the chroot is not in the PATH .

```bash
export PATH=/bin:/sbin:$PATH
wwmkchroot ubuntu14.04-64-srv /opt/ohpc/admin/images/i86_x64/client02
```

### Configuration To Deploy

The [Warewulf] database needs to be changed to allow the installation of the
chroot file system onto the disk.

- The options are written via `object modify -s VARIABLE=VALUE`

- The options are read via `wwsh object print nodename -p :all`

#### Boot Loader Location

```bash
wwsh object modify -s bootloader=sda client01
NAME             BOOTLOADER
======================================================
client01          sda

About to apply 1 action(s) to 1 object(s):

    SET: bootloader   = UNDEF -> "sda"

    Proceed?
    Yes/No [no]>
```

#### Disk Partition

Similar to boot loader location, the following variables need to be set:

- DISKPARTITION: what disk holds the partition table

```bash
wwsh object modify -s diskpartition=sda client01
```
#### Format Disk

Similar to boot loader location, the following variables need to be set:

- DISKFORMAT : what partitions need to be formatted

```bash
wsh object modify -s diskformat=sda1 client01
```
#### File Systems

Similar to boot loader location, the following variables need to be set:

- FILESYSTEMS : what file systems should be used for which partition, what
  size, what type and where to mount it

```bash
wwsh object modify -s FILESYSTEMS="mountpoint=/boot:dev=sda1:type=ext3:\
size=500,dev=sda2:type=swap:size=32768,mountpoint=/:dev=sda3:type=ext4:\
size=fill" client01
```

#### Install Node Or Boot Locally (Disk)

Set node in INSTALL mode

```bash
wwsh provision set --bootlocal=UNDEF
```

Set node in LOCAL boot mode (boot from disk)

```bash
wwsh provision set --bootlocal=EXIT
```

When this variable value is displayed it is displayed as

```
client01: BOOTLOCAL = TRUE
```

**Example:**

```bash
provision set --bootlocal=EXIT client01
provision print client01
[...]
client01: BOOTLOCAL = TRUE
[...]
```

###  Creating VNFS From Chroot

Create new one for Ubuntu

```bash
wvnfs client02 --chroot=/opt/ohpc/admin/images/i86_x64/client02
reating VNFS image from client02
Building new chroot...
Building and compressing the final image
Cleaning temporary files
VNFS ’client02’ has been imported
Done.
Wrote a new configuration file at: /etc/warewulf/vnfs/client02.conf
```

Let a node use it

```bash
provision set --vnfs client02 client01
```

### Creating Bootstrap

See current bootstrap files:

```bash
wwsh bootstrap list
BOOTSTRAP NAME SIZE (M)
```

Create new one for Ubuntu

```bash
wwbootstrap --chroot /opt/ohpc/admin/images/i86_x64/client02 3.13.0-24-generic
Number of drivers included in bootstrap: 748
Number of firmware images included in bootstrap: 17
Building and compressing bootstrap
Integrating the Warewulf bootstrap: 3.13.0-24-generic
Including capability: provision-adhoc
Including capability: provision-files
Including capability: provision-selinux
Including capability: provision-vnfs
Including capability: setup-filesystems
Including capability: transport-http
Compressing the initramfs
Locating the kernel object
Bootstrap image ’3.13.0-24-generic’ is ready
Done.
```

Let a node use it

```bash
provision set --bootstrap=3.13.0-24-generic client01
```

See it

```bash
provision list client01
NODE                VNFS          BOOTSTRAP               FILES
===============================================================================
client01             client02        3.13.0-24-generic       dynamic_hosts,grou...
```

Set install mode

```bash
provision set --bootlocal=0 client01
provision print client01
  #### client01 ################################################################
client01: BOOTSTRAP     = 3.13.0-24-generic
client01: VNFS          = client02
client01: FILES         = dynamic_hosts,group,munge.key,network,passwd,shadow,\
                         slurm.conf
client01: PRESHELL      = FALSE
client01: POSTSHELL     = TRUE
client01: CONSOLE       = UNDEF
client01: PXELINUX      = UNDEF
client01: SELINUX       = DISABLED
client01: KARGS         = "quiet"
client01: FILESYSTEMS   = mountpoint=/boot:dev=sda1:type=ext3:size=1024,\
                         dev=sda2:type=swap:size=32768,mountp
client01: DISKFORMAT    = sda1,sda2,sda3
client01: DISKPARTITION = sda
client01: BOOTLOADER    = sda
client01: BOOTLOCAL     = FALSE
```

### Trouble Shooting

[Warewulf] provides additional variables, setting or reading them can help
trouble shoot the installation.

```
PRESHELL
POSTSHELL
IFRENAME
CONSOLE
```

### Dispatch (Set/Change/Upate) A Node To One Bootstrap And VNFS Image

```bash
wwsh -y provision set "${compute_regex}" --vnfs=centos7.2 \
--bootstrap=‘uname -r‘ \
--files=dynamic_hosts,passwd,group,shadow,slurm.conf,munge.key,network
```

**Example Grooveport:**

```bash
wwsh -y provision set n000 --vnfs=centos7.2 --bootstrap=‘uname -r‘ \
--files=dynamic_hosts,passwd,group,shadow,slurm.conf,munge.key,network
```

**Example Adamas Pass:**

```bash
wwsh -y provision set n001 --vnfs=centos7.2 \
--bootstrap=3.10.0-327.36.2.el7.x86_64 \
--files=dynamic_hosts,passwd,group,shadow,slurm.conf,munge.key,network
```

### Dispatch (Set/Change/Upate) A Node To One VNFS Image

```bash
wwsh -y provison set n000 --vnfs=centos7.2-testing
```

### Apply Changes

```bash
systemctl restart dhcpd
wwsh pxe update
```

## DHCP

### Mixing manually dhcpd.conf and Warewulf

Changing /etc/dhcp/dhcpd.conf will not service configuration made in
[Warewulf]. However it is possible to change the Warewolf template.

`/etc/warewulf/dhcpd-template.conf`

It is possible to add

```
include "/etc/dhcp/manual/subnet.conf";
include "/etc/dhcp/manual/node.conf";
```

## Adding Ganglia - 1

This is the first attempt and it does __NOT__ work at the moment.

Ganglia needs to be added on the front node and on the clients.

### Install Ganglia to Front Node

Make sure selinux is disabled! Installation works with seleinux, but if selinux
is enabled ganglia ca not collect data. (There was an error collecting ganglia
data (127.0.0.1:8652): fsockopen error: Permission denied)

```bash
setenforce 0
yum -y groupinstall ohpc-ganglia
```

### Install Ganglia on the Clients

Backup old configuration

```bash
cp /etc/ganglia/gmond.conf /etc/ganglia/gmond.conf.‘date +’%F’‘
cp /opt/ohpc/pub/examples/ganglia/gmond.conf /etc/ganglia
```

Source the environment

```bash
source /opt/kuelker/env/ohpc ADAMSPASS
```

Set the server name

```bash
perl -pi -e "s/<sms>/${OHPC_SERVER_NAME}/" /etc/ganglia/gmond.conf
```

Set the site name

```bash
cp /etc/ganglia/gmetad.conf /etc/ganglia/gmetad.conf.‘date +’%F’‘
echo "gridname $OHPC_SITE_NAME" >> /etc/ganglia/gmetad.conf
```

Enable and start Ganglia

```bash
systemctl enable gmond
systemctl enable gmetad
systemctl start gmond
systemctl start gmetad
systemctl try-restart httpd
```

### Install Ganglia on the Clients

```bash
source /opt/kuelker/env/ohpc ADAMSPASS
yum -y --installroot=$OHPC_SERVER_CHROOT_TEST install ganglia-gmond-ohpc
chroot $OHPC_SERVER_CHROOT_TEST systemctl enable gmond
```

## Documentation

The official install guide:

- [openhpc.community]
- [ftp.vcu.edu/pub]

## Technical Descriptions

The features like distributing a file to the node via the file storage, seems
to be enabled by so called capabilities for which each a file has to be present
in `/srv/warewulf/initramfs/capabilities`.  The content of this seems to be
`cpio` archives and not so important. But one level higher there is a `cpio`
archive called base. This contains a whole `initrd` `busybox`. Also including
Warewolf related components like `wwgetfiles -> transporthandler`.
Transporthandler is a shell wrapper script. That redirects to something out
site of that `cpio` archive with pathes starting with

```
/warewulf/transports/$WWTRANSPORT/$PROG
```
**Example:**

```
/warewulf/transports/http/wwgetfiles PARAMERTERS
```

The http method uses wget to get files like this

```bash
wget -q -O $TMPFILE \
"http://$master/WW/file?hwaddr=$WWINIT_HWADDR&timestamp=$TIMESTAMP" 2>/dev/null
```

It turns to be out that this files are in the cpio archive
`cpabilities/transport-http`.

```
functions register wwgetfiles wwgetnodeconfig wwgetscript wwgetvnfs
```

#### file.pl

```bash
wget http://gw200/WW/file?hwaddr=00:10:71:02:c7:2e
3 network 0 0 0644 1477052827 /etc/sysconfig/network
20 passwd 0 0 0644 1486657890 /etc/passwd
21 group 0 0 0644 1486657890 /etc/group
22 shadow 0 0 0000 1486657890 /etc/shadow
23 munge.key 200 200 0400 1486657890 /etc/munge/munge.key
24 slurm.conf 0 0 0644 1486657890 /etc/slurm/slurm.conf
5 dynamic_hosts 0 0 0644 1496238363 /etc/hosts
```

#### nodeconfig.pl

```bash
wget http://gw200/WW/nodeconfig?hwaddr=00:10:71:02:c7:2e&timestamp=2017-03-31
WW_ID="133" export WW_ID WWBOOTLOCAL="-1" export WWBOOTLOCAL\
WW_HWADDR="00:10:71:02:c7:2e" export WW_HWADDR WW_IPADDR="172.16.100.1"\
export WW_IPADDR WWBOOTLOADER="sda" export WWBOOTLOADER WW_TYPE="node"\
export WW_TYPE WWDISKFORMAT="sda1,sda2,sda3" export WWDISKFORMAT\
WWKARGS="noacpi" export WWKARGS WWBOOTSTRAPID="138" export WWBOOTSTRAPID\
WWNAME="client01" export WWNAME WW_TIMESTAMP="1496238361" export WW_TIMESTAMP\
WWNODENAME="client01" export WWNODENAME WWVNFSID="137" export WWVNFSID\
WWNETDEVS="eth0" export WWNETDEVS WW__NETDEVLIST="eth0=172.16.100.1"\
export WW__NETDEVLIST WWFILESYSTEMS="mountpoint=/boot:dev=sda1:type=ext3:\
size=1024,dev=sda2:type=swap:size=32768,mountpoint=/:dev=sda3:type=ext3:\
size=100000" export WWFILESYSTEMS WWDISKPARTITION="sda" export WWDISKPARTITION\
WWFILEIDS="5,21,23,3,20,22,24" export WWFILEIDS
```

### How to test a node configuration?

```bash
$MASTER = IP of warewolf master server
wget http://$MASTER/WW/nodeconfig?hwaddr=00:1e:67:f9:4e:ce
```

This basically exports a shell environment that can be sourced

### What is this Warewulf bootstrap?

When a [Warewulf] bootrap is exported it is a file with the ending .wwbs, which
probably should mean warewulf bootstrap . This is basically a gzip file. It can
be uncompressed with gunzip. What comes out of this is a cpio archive which
contains the following:

```
initramfs
kernel
```

The initramfs is a directory that contains:

```
etc
lib
```

The etc directory contains just 1 file: `wwmodprobe` The content are modules
(and parameters) that will be called with `modprobe`. The execution of the
content can be seen in the 2nd line of a [Warewulf] boot screen:

"Loading drivers: uhci-hcd ohci-hcd, ...."

This file might be constructed by `/etc/warewulf/bootstrap.conf`

In the lib directory one can find firmware and kernel modules. No
configuration.

### What means "Checking for network device: eno2 SKIPPED"?

In the base `cpio` archive there is the file `init` that basically is a shell
script after the first kernel starts.  It print this line. After it prints its
line it tries for the variable `$DEVICE` to bring up the device with `ifconfig`
`$DEVICE` up. If this is not possible to goes to the `wwskipped` function that
prints the gray SKIPPED word.

There can be many reasons why this can fail. One obvious reason is that the
device name do not exists.

When the board has network names like eno1, eno2 [Warewulf] will overwrite just
(only) the device that is configured with a different name, like eth1. So in
the list looks strange:

```bash
ifconfig -a |grep flags
eno1: flags=4098<BROADCAST,MULTICAST> mtu 1500
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 1500
lo: flags=73<UP,LOOPBACK,RUNNING> mtu 65536
```

### Tweaking Warewulf Provision

The function of the [Warewulf] provision are in CPIO archives.

#### Making a Backup

```bash
cp -a /srv/warewulf /srv/warewulf.‘date +’%FT%T’‘
```

#### Extracting the Archive

```bash
mkdir /tmp/WORK
cd /tmp/WORK
cpio -vict < /srv/warewulf/initramfs/capabilities/setup-filesystems
cpio -idv < /srv/warewulf/initramfs/capabilities/setup-filesystems
```

Change the content.

#### Update the Archive

Check the Content

```bash
cd /tmp/Work
find .
./warewulf
./warewulf/provision
./warewulf/provision/80- mkbootable
./warewulf/provision/99-postreboot
./warewulf/provision/95-umount
./warewulf/provision/20- filesystems
```

Update by creating a new archive

```bash
md5sum /srv/warewulf/initramfs/capabilities/setup-filesystems
02749848b4f4016c397d4f86903902d7 \
/srv/warewulf/initramfs/capabilities/setup-filesystems
cd /root/TWEAKING-WAREWULF/initramfs/capabilities/setup-filesystems
find warewulf
| cpio -ov > /srv/warewulf/initramfs/capabilities/setup-filesystems
warewulf
warewulf/provision
warewulf/provision/80-mkbootable
warewulf/provision/99-postreboot
warewulf/provision/95-umount
warewulf/provision/20-filesystems
37 blocks
[root@$HOST WORK]# md5sum /srv/warewulf/initramfs/capabilities/setup-filesystems
edb8f73c44b8a1875d1b12fae0a6e1ae \
/srv/warewulf/initramfs/capabilities/setup-filesystems
```

#### Make changes Active

To actually make the changes active a new boostrap image has to be created

```bash
wwbootstrap --chroot /opt/ohpc/admin/images/i86_x64/client02 3.13.0-24-generic
Number of drivers included in bootstrap: 748
Number of firmware images included in bootstrap: 17
Building and compressing bootstrap
WARNING: Do you wish to overwrite ’3.13.0-24-generic’ in the Warewulf data store?
Yes/No> yes
Integrating the Warewulf bootstrap: 3.13.0-24-generic
Including capability: provision-adhoc
Including capability: provision-files
Including capability: provision-selinux
Including capability: provision-vnfs
Including capability: setup-filesystems
Including capability: setup-filesystems.2017-05-26
Including capability: transport-http
Compressing the initramfs
```

#### Creating the Archive

```bash
ls | cpio -ov > directory.cpio
find . -print -depth | cpio -ov > tree.cpio
```

### How to use the --netrename command line option?

```bash
wwsh node set -D eno2 --netrename=eth1 a4kn048
```

## PXE boot over frontpanel

To configure WW to boot over front panel:
eth0 backplane MAC0 eth1 front panel MAC1
Usually you do:
```bash
bin/wwsh-add-new-node-via-backplane NR MAC0
```

Disable first entry in DHCP

```
  # MAC0
MAC1
MAC2
MAC3
```
However you can do:

```bash
bin/wwsh-add-new-node-via-backplane NR MAC1
```

Disable first AND SECOND entry in DHCP

```
  # MAC0
  # MAC1
MAC2
MAC3
```

## wwlogger

This section was __NOT__ tested. Looking at the source code, it might work.
Feedback is welcome.

The wwlogger call can be used in Warefulf provision scripts to log to the
provision server.

It logs to /var/log/messages , example:

```
Apr 12 11:37:53 c1.localdomain wwlogger: Running provision script: mkbootable
Apr 12 11:37:53 c1.localdomain wwlogger: Provision script error: mkbootable (RETVAL=2)
```

It uses `/usr/bin/logger` or similar to add message to the log system via wget
calls from the client.

```
http://172.16.254.254/WW/logger?hwaddr=00:00:00:00:00:00&log=HALLO/
```

```
[Warewulf]::Logger
```

log level

```
CRITICAL (0), ERROR (1), WARNING (2), NOTICE (3), INFO (4), or DEBUG (5)
```

## Receipts

Custom Kernel System Deploy

```
CHROOT=/opt/ohpc/admin/images/i86_x64/client03
```

1. Compile a Kernel

2. Copy over relevant files from /boot to $CHROOT .

```bash
cp config-4.8.7 $CHROOT/boot
cp initrd.img-4.8.7 $CHROOT/boot
cp System.map-4.8.7 $CHROOT/boot
cp vmlinuz-4.8.7 $CHROOT/boot
```

3. Copy over modules to $CHROOT

```bash
rsync -aSP lib/modules/4.8.7 $CHROOT/lib/modules
rsync -aSP var/lib/initramfs-tools/4.8.7 $CHROOT/var/lib/initramfs-tools
```

If `lib/modules` are not copied over `$CHROOT//lib/modules/4.8.7/modules.dep`
will not be found and drivers not be added. The system will not likely be
correctly provisioned (no ethernet interface) unless the drivers are compiled
in.

```bash
wwbootstrap -c $CHROOT 4.8.7
Number of drivers included in bootstrap: 799
Number of firmware images included in bootstrap: 17
Building and compressing bootstrap
WARNING: Do you wish to overwrite ’4.8.7’ in the Warewulf data store?
Yes/No> Yes
Integrating the Warewulf bootstrap: 4.8.7
Including capability: provision-adhoc
Including capability: provision-files
Including capability: provision-selinux
Including capability: provision-vnfs
Including capability: setup-filesystems
Including capability: transport-http
Compressing the initramfs
Locating the kernel object
Bootstrap image ’4.8.7’ is ready
Done.
```

If there was no limit in Modules (1.3 GB for kernel 4.8.7) image creation will
take time. The boostrap without modules will be just 7.8MB in the data store,
while with modules 327.1MB!

```bash
wwvnfs -y --chroot /opt/ohpc/admin/images/i86_x64/client03
```

This takes 16 min and the image with generic Ubuntu Kernel as 208MB in storage
the new one has now 1400MB in storage.

### POSTREBOOT

```bash
object modify -s postreboot=TRUE client01
```

### FILES

The file storage (access via wwsh file).

#### add a file

```bash
wwsh file import /opt/ohpc/admin/files/passwd-ubuntu-14.04
```

#### list a file

```bash
wwsh file list passwd-ubuntu-14.04
passwd-ubuntu-14.04 : -rw-r--r-- 1 root root 1121 passwd-ubuntu-14.04
```

#### Meta information

```bash
wwsh file print passwd-ubuntu-14.04

  #### passwd-ubuntu-14.04 ####################################################
passwd-ubuntu-14.04: ID = 143
passwd-ubuntu-14.04: NAME = passwd-ubuntu-14.04
passwd-ubuntu-14.04: PATH = /opt/ohpc/admin/files/passwd-ubuntu-14.04
passwd-ubuntu-14.04: ORIGIN = /opt/ohpc/admin/files/passwd-ubuntu-14.04
passwd-ubuntu-14.04: FORMAT = data
passwd-ubuntu-14.04: CHECKSUM = 82e57ba08a70e6d122614061f602a40e
passwd-ubuntu-14.04: INTERPRETER = UNDEF
passwd-ubuntu-14.04: SIZE = 1121
passwd-ubuntu-14.04: MODE = 0644
passwd-ubuntu-14.04: UID = 0
passwd-ubuntu-14.04: GID = 0
```

#### Changing

```bash
wwsh file set passwd-ubuntu-14.04 --path=/etc/passwd
wwsh file print passwd-ubuntu-14.04
  #### passwd-ubuntu-14.04 ####################################################
passwd-ubuntu-14.04: ID = 143
passwd-ubuntu-14.04: NAME = passwd-ubuntu-14.04
passwd-ubuntu-14.04: PATH = /etc/passwd
passwd-ubuntu-14.04: ORIGIN = /opt/ohpc/admin/files/passwd-ubuntu-14.04
passwd-ubuntu-14.04: FORMAT = data
passwd-ubuntu-14.04: CHECKSUM = 82e57ba08a70e6d122614061f602a40e
passwd-ubuntu-14.04: INTERPRETER = UNDEF
passwd-ubuntu-14.04: SIZE = 1121
passwd-ubuntu-14.04: MODE = 0644
passwd-ubuntu-14.04: UID = 0
passwd-ubuntu-14.04: GID = 0
```

For `/etc/shadow` for example the group shadow id is 42 in /etc/group

```bash
wwsh file set shadow-ubuntu-14.04 --gid=42
```

#### Deleting A File

```bash
file delete passwd-ubuntu-14.04
Are you sure you want to delete 1 files(s):

    DEL: FILE = passwd-ubuntu-14.04

    Yes/No> yes
```

##  Ad-Hoc Files

Ad-hoc files are a little undocumented in [Warewulf]. The file can be script
that is executed in the busy box as `sh`. The root file system of the new
system is reachable under the path `/newroot`.

## Glossary

**bootstrap , bootstrap image**: This is the boot loader on top of PXE. This
will prepare to fetch the **VNFS** image to be either (a) booted in memory
(Diskless) or (b) to write on disk (Stateless).  When a node is provisioned
using [Warewulf], it initially obtains a small bootstrap image from the master
during the PXE process (transferred using TFTP). This bootstrap image includes
a Linux kernel, device drivers, and a minimal set of programs to complete the
provisioning process. Once the bootstrap is in place, it calls back to the
master, downloads the **VNFS** capsule, and provisions the file system. It then
calls init for the new file system to bring up the desired Linux system.

**VNFS**: The Virtual Node File System is the file system template or image
used for for booting many systems. This is the actual operating system image
that will be provisioned to a system, resides in a chroot environment (**VNFS**
Template).

**Datastore**: The data for [Warewulf] is stored in the file system of the head
node and in the Mysql/MariaDB of the head node.

**Nodes**: Nodes are stored within [Warewulf] as data store objects of type
"node". Nodes can be generally manipulated with the node [Warewulf] command.
The [Warewulf] provision package also includes a helper command called
provision which facilitates setting up nodes for provisioning.  Node provision
configuration needs:

- vnfs (file system image)
- boostrap (kernel)
- netdevs (netdev, ip, mac)

**provisioning**: helps nodes to  be deployed with files

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.3   | 2024-07-25 | Quick guide format                                   |
| 0.1.2   | 2021-01-02 | Improve node names                                   |
| 0.1.1   | 2020-05-19 | Merge with Warewulf content from 2018                |
| 0.1.0   | 2017-05-19 | Initial release                                      |

[ftp.vcu.edu/pub]: ftp://ftp.vcu.edu/pub/linux/openhpc/
[openhpc]: https://openhpc.community/
[openhpc.community]: http://www.openhpc.community/downloads/
[Stateful Provisioning]: http://warewulf.lbl.gov/trac/wiki/Recipes/StatefulProvisioning
[Warewulf]: http://warewulf.lbl.gov/trac/wiki/Documentation
