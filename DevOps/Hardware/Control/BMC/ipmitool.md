---
title: Ipmitool
author: Christian Külker
date: 2023-05-22
version: 0.1.4
type: doc
keywords:
    - ipmitool
categories:
    - BMC
tags:
    - ipmitool
    - IPMI
    - BIOS
    - SDR
    - SEL
    - FRU
    - SOL
    - nmap
    - DHCP
description: Interaction with the Board Management Controller

---

The `ipmitool` command line tool is used to interact with a __Board Management
Controller__ (`BMC`) through the **Intelligent Platform Management Interface**
(`IPMI`).  IPMI is an open standard for monitoring, logging, and controlling
hardware that is implemented independently of the main CPU, BIOS, and OS via
sideband access. On some platforms, `IPMI` can also be used to manage hardware
inventory information.

* `SDR` - Sensor Data Repository
* `SEL` - System Event Log
* `FRU` - Field Replaceable Unit

## Install

```bash
aptitude install ipmitool
```

When using, make sure some kernel modules are loaded.

```
ipmi_devintf
ipmi_msghandler
ipmi_poweroff
ipmi_si
ipmi_watchdog
```

## Set DHCP

```bash
ipmitool lan set 1 ipsrc dhcp
```

## Reset the BMC

```bash
ipmitool mc reset cold
```

Or

```bash
ipmitool mc reset warm
```

## BMC Sensor List

If the `BMC` supports it (this depends on the `OEM`), sensors can be listed.
Some sensors may be on the `BMC` SOC, but most likely these sensors are made
available to the `BMC` in various ways. Usually the `OEM` defines if the value
is in or out of specification. Some values are dependent on the `CPU` `OEM`.

```bash
ipmitool -I open sdr list
ACPI State       | 0x01              | ok
LPC Reset        | 0x01              | ok
Chassis          | 0x01              | ok
BMC RST          | 0x00              | ok
CPU0PECI         | 42 degrees C      | ok
CPU1PECI         | 38 degrees C      | ok
+1.1V            | 1.11 Volts        | ok
+1.4V            | 1.41 Volts        | ok
+0.9V            | 0.91 Volts        | ok
+3.0V            | 2.98 Volts        | ok
+1.5V            | 1.51 Volts        | ok
+2.5V            | 2.42 Volts        | ok
CPU0VCCP         | 0.97 Volts        | ok
CPU1VCCP         | 0.96 Volts        | ok
DDRCPU0          | 0.75 Volts        | ok
DDRCPU1          | 0.74 Volts        | ok
+1.8VICH         | 1.79 Volts        | ok
+1.5VICH         | 1.55 Volts        | ok
```

## Set The NAC Address

Setting the MAC address if the `BMC` can be done via the `OS`.

```bash
ipmitool lan set <channel> macaddr <macaddr>
```

Sometimes the `MAC` address can only be set by vendor specific raw commands.
Sometimes the `BMC` has 2 `LAN` interfaces. The number depends on the hardware
and the `BMC` firmware. The channel number usually needs to be specified.

```bash
ipmitool raw 0x0c 0x01 0x01 0xc2 0x00
ipmitool lan set 1 macaddr aa:bb:cc:dd:ee:ff
ipmitool lan print 1

ipmitool raw 0x0c 0x01 0x08 0xc2 0x01
ipmitool lan set 8 macaddr a0:42:3f:2c:61:a2
ipmitool lan print 8
```

## Activate SOL

To enable **Serial Console Over LAN** (`SOL`). If the `OEM` has configured the
`BMC` in a secure way, a **user** and **password** are required. Since this is
usually not changed, this can be a security problem.

```bash
ipmitool -I lanplus -H $REMOTEHOST -U $USER -P $PASSWORD sol activate
ipmitool -I lanplus -H $REMOTEHOST -U $USER -P $PASSWORD -v sol info
```

## Debug SOL

To debug the setup of **Serial Over Lan** (`SOL`), add the `-v` option to
enable verbose output. verbose output and in some cases set the privilege level
explicitly with the `-L` option.

```bash
ipmitool -v -L USER -I lanplus -H 172.20.50.102 -U admin -P admin sol activate
```

If this does not help use `namp`. See [ipmitool Fails with Authentication
Issues](http://h20564.www2.hpe.com/hpsc/doc/public/display?docId=emr_na-c03481170)

## Raw Commands

Sometimes the `OEM` implements undocumented features in the `BMC` firmware that
can be accessed via the **raw** option. Since these commands are not standard,
it depends on the `BMC` what the result will be. This is just an example.

```bash
ipmitool raw 0x36 0xc
```

## Testing

Some `OEMs` implement meaningful self tests. However, dummy self tests that
always pass have been seen in the wild.

```bash
ipmitool mc selftest
Selftest: passed
```

## Links

* [ipmitool source](https://github.com/ipmitool/ipmitool)
* [IPMI v2.0 Rev 1.1](https://www.intel.com/content/dam/www/public/us/en/documents/product-briefs/ipmi-second-gen-interface-spec-v2-rev1-1.pdf)

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.4   | 2023-05-22 | Improve writing, move history                        |
| 0.1.3   | 2022-06-30 | Formatting, changes->history, shell->bash            |
| 0.1.2   | 2020-05-03 | Debug `SOL`                                          |
| 0.1.1   | 2020-05-02 | Set MAC address raw                                  |
| 0.1.0   | 2016-02-12 | Initial release                                      |

