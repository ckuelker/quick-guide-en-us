---
title: VLAN under Debian
linkTitle: VLAN
author: Christian Külker
version: 0.1.1
date: 2024-07-20
type: doc
locale: en_US
lang: en
disclaimer: True
toc: True
categories:
- Network
commands:
- vconfig
- ifconfig
- modprobe
- lsmod
- ifup
- ip
- systemctl
tags:
- vlan
- VID
- 8021q
- IEEE 802.1Q
description: VLAN configuration under Debian

---

## Introduction

Virtual Local Area Networks (VLANs) represent a fundamental technology in
network segmentation, allowing distinct broadcast domains to be cohesively
managed within a single physical infrastructure. This guide outlines the
configuration of VLANs under the Debian operating system, detailing procedural
steps from installation to setup options. By leveraging Debian's robust
networking capabilities, administrators can effectively delineate network
traffic, enhance security. The following sections provide a brief walk-through
for installing the necessary packages, loading relevant kernel modules (if
needed), and configuring network interfaces in two different modes.


### VLAN identifier (VID)

A 12-bit field specifies the VLAN the frame belongs to. Values 0 and 4095
(0x000 and 0xFFF in hexadecimal) are reserved. All other values can be used as
VLAN identifiers, allowing up to 4,094 VLANs. The reserved value 0x000
indicates the frame does not carry a VLAN ID. On bridges, VID 0x001 (the
default VLAN ID) is often reserved for network management VLANs. The VID value
0xFFF is reserved for implementation use and must not be configured or
transmitted. It can indicate a wildcard match in management operations or
filtering database entries.
[Wikipedia](https://en.wikipedia.org/wiki/IEEE_802.1Q)

## Installation

Debian and its derivatives use the vlan package to provide the necessary kernel
modules and utilities. Install it if it's not already present:

```bash
aptitude install vlan
```

## Setup

Eventually, the driver needs to be loaded into the kernel.

```bash
modprobe 8021q
echo 8021q >> /etc/modules
lsmod | grep 8021q
```

IEEE 802.1Q, commonly known as Dot1q, is a standard for implementing VLANs on
Ethernet networks. It outlines how Ethernet frames are tagged for VLANs,
including handling procedures for network devices like bridges and switches.
The standard includes a quality-of-service (QoS) prioritization called IEEE
802.1p and the Generic Attribute Registration Protocol. On VLAN-aware sections
of a network, frames receive a VLAN tag to indicate their membership. Untagged
frames in these areas default to the native VLAN. Developed by the IEEE 802.1
working group, the standard is regularly updated, incorporating advancements
such as IEEE 802.1ad and IEEE 802.1s. The 2014 update integrated the IEEE
802.1D-2004 standard.

[Wikipedia](https://en.wikipedia.org/wiki/IEEE_802.1Q)

## Configuration

For configuring a VLAN on Debian systems with or without using the vconfig
command, you'll typically rely on the native support within the Linux kernel
and the network interface configuration files. The method involves directly
editing the `/etc/network/interfaces` file to define VLANs using the physical
network interface. Below, I'll demonstrate how to configure a VLAN (e.g., VLAN
ID 500) on a network interface (e.g., eth1) using this approach in four
different ways.

### Command Line

```bash
ip link add link eth1 name eth1.500 type vlan id 500
ip link set dev eth1.500 up
ip addr add 192.168.50.254/24 dev eth1.500
```

### Method A: [tested]

#### Static

```bash
vim /etc/network/interfaces
```

~~~
     auto eth1.500
     iface eth1.500 inet static
     address 192.168.50.254
     netmask 255.255.255.0
~~~

#### Manual

```bash
vconfig add eth1 500
Added VLAN with VID == 500 to IF -:eth1:-
ifconfig eth1.500 192.168.50.254/24
ifup eth1.500
```

### Method B: [untested]

#### Static

```bash
vim /etc/network/interfaces
```

~~~
     auto vlan500
     iface vlan500 inet static
     address 192.168.50.254
     netmask 255.255.255.0
     vlan_raw_device eth1
~~~

#### Manual

```bash
vconfig add eth1 500
```

### Method C: (Combining A and B) [untested]

```bash
vim /etc/network/interfaces
```

~~~
auto eth1.500
iface eth1.500 inet static
    address 192.168.50.254
    netmask 255.255.255.0
    vlan-raw-device eth1
~~~

- `auto eth1.500`: This ensures the interface is brought up at boot.
- `iface eth1.500 inet static`: Specifies a static IP configuration for the
  VLAN interface.
- `address 192.168.50.254`: Sets the IP address for the VLAN interface.
- `netmask 255.255.255.0`: Sets the netmask for the VLAN interface.
- `vlan-raw-device eth1`: Specifies the underlying physical interface to which
  the VLAN is attached.

After editing the file, restart the networking service to apply the changes:

```bash
systemctl restart networking
# OR
ifup eth1.500
```

By following one of these steps, you will have configured VLAN ID 500 on eth1
with the specified IP address and netmask in Debian.

Debian recognizes the VLAN ID from the interface name. The convention
`eth1.500` indicates that this is a VLAN interface with ID 500 on the physical
interface eth1.

Interface Naming Convention:

- `eth1` is the physical interface.
- `.500` is the VLAN ID appended to the physical interface name, creating
  `eth1.500`.

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2024-07-20 | Github release, minor fixes                          |
| 0.1.0   | 2019-11-21 | Initial release                                      |


