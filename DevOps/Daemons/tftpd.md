---
linkTitle: Tftpd
title: Trivial File Transfer Daemon
type: doc
author: Christian Külker
date: 2023-05-17
version: 0.1.2
keywords:
    - TFTP
    - TFTPD
categories:
    - Tftpd
tags:
    - TFTPD
    - HPA TFTPD
    - PXE
    - BMC
    - SOC
    - CentOS
    - Debian
    - xinetd
---

In some cases, a Trivial File Transfer Protocol Daemon (tftpd) comes in handy.
One case where it is almost mandatory is when booting systems using the Intel
Pre-boot Execution Environment (PXE). But also for some very simple devices
like embedded systems or Systems On Chip (SOC) like Board Management Controller
(BMC), the TFTP daemon is sometimes the only method to copy a file to the
device. This guide focuses on CentOS.

## TFTP Server On CentOS

Check if the server is already installed

```bash
rpm -q tftp-server
tftp-server-0.49-2.el5.centos
```

Activate the daemon for some run-levels via xinetd

```bash
/sbin/chkconfig --level 245 xinetd on
/sbin/chkconfig --level 345 tftp on
```

Setting the verbose level

```bash
vim  /etc/xinet.d/tftp
```

Add `-vvv` to '`server_args`' that will help the initial debugging.

## HPA TFTP Server On Debian

Install the HPA TFTP Server. HPA stands for Hans Peter Anvin, who maintains
this version. Back in 1995 this was the best alternative of TFTP servers, as
others were quite buggy. There is also a `tftpd` package in Debian.
Configuration and supported protocols may differ between flavors of TFTP
servers.

```bash
aptitude install tftpd-hpa
```

### Install DHCPD

```bash
aptitude install dhcp3-server
```

### Configure DHCPD

```bash
cd /etc/dhcp3
cp dhcpd.conf dhcpd.conf-original
vim dhcpd.conf
```

```
option domain-name-servers 208.67.222.222, 208.67.220.220;

    subnet 10.0.10.0 netmask 255.255.255.0 {
        range 10.0.10.10 10.0.10.15;
        filename "pxelinux.0";
        next-server 10.0.10.150;
        option routers 10.0.10.150;
    }

```

### Configure TFTPD For CentOS

The boot directory is at `/var/lib/tftpboot`

```bash
mkdir -v /var/lib/tftpboot/pxelinux.cfg
mkdir -vp /var/lib/tftpboot/centos6/i386 /var/lib/tftpboot/centos6/x86_64 
rsync -av rsync.hrz.tu-chemnitz.de::ftp/pub/linux/centos/6/os/i386/images/\
pxeboot/ /var/lib/tftpboot/centos6/i386
rsync -av rsync.hrz.tu-chemnitz.de::ftp/pub/linux/centos/6/os/x86_64/\
images/pxeboot/ /var/lib/tftpboot/centos6/x86_64/
touch /var/lib/tftpboot/pxelinux.cfg/default
vim /var/lib/tftpboot/pxelinux.cfg/default
```

Add this content:

~~~
DISPLAY boot.txt

DEFAULT  centos6_x86_64_install

MENU TITLE PXE Menu

LABEL centos6_x86_64_install
kernel centos6/x86_64/vmlinuz
append vga=normal initrd=/centos6/x86_64/initrd.img devfs=nomount
#append vga=normal initrd=/centos6/x86_64/initrd.img devfs=nomount\
ramdisk_size=9216

LABEL centos6_i386
kernel /centos6/i386/vmlinuz
append initrd=/centos6/i386/initrd.img devfs=nomount
#append initrd=/centos6/i386/initrd.img devfs=nomount ramdisk_size=9216

PROMPT 1
TIMEOUT 10
~~~

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2023-05-17 | Improve writing                                      |
| 0.1.1   | 2022-06-18 | History, shell->bash                                 |
| 0.1.0   | 2016-06-22 | Initial release                                      |

