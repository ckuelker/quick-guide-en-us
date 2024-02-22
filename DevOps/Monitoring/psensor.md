---
title: psensor
author: Christian Külker
version: 0.1.1
date: 2024-02-11
type: doc
locale: en_US
lang: en
disclaimer: True
toc: True
categories:
- Monitoring
commands:
- psensor
tags:
- psensor
discription: A tool for measuring sensors, display them and issue alarms

---

## Introduction

The _psensor_ package is a graphical user interface for sensors that monitor
the system from the desktop. It is not intended for automatic monitoring
without configuration. And while it is possible to configure a log file, the
primary purpose is to monitor a given system live. For example, to understand
under what conditions it crashes: to see temperature or power curves.

## Installation

On Debian 12 Bookworm, the package manager can be used to install the _psensor_
package.

```bash
aptitude install psonsor
```

## Configuration

Since this is a GUI, the best way to configure it is to start the tool and
click on `Psensor->Preferences` and/or `Psensor->Sensor Preferences`.
Basically `Sensor Preferences` is a subdialog of the `Preferences` dialog.
Even without much effort, this tool can be useful by enabling only one sensor,
set a temperature in degrees Celsius and enable the desktop alarm.


An advanced option is to configure a script to be executed in case of an alarm.

## LM-Sensors

While _psensor_ basically uses _lm-sensors_ to read system sensors, there are
some minor differences. First, not all _lm-sensors_ are present, second,
sensors from other sources like `NVIDIA` `GPUs` and hard disk monitoring are
also included. So in that sense it can actually be more useful.

### Comparison

| System                | LM Sensors            | psensor  |
| --------------------- | --------------------- | -------- |
| fam15h_power-pci-00c4 | power1:      46.59 W  |          |
| k10temp-pci-00c3      | temp1:       +30.4°C  |  +30°C   |
| it8721-isa-0290       | in0:          2.80 V  |          |
|                       | in1:          2.80 V  |          |
|                       | in2:        828.00 mV |          |
|                       | +3.3V:        3.29 V  |          |
|                       | in4:        792.00 mV |          |
|                       | in5:          2.53 V  |          |
|                       | in6:        204.00 mV |          |
|                       | 3VSB:        72.00 mV |          |
|                       | Vbat:         3.29 V  |          |
|                       | fan1:       1839 RPM  | 1839 RPM |
|                       | fan2:        844 RPM  |  844 RPM |
|                       | fan3:          0 RPM  |    0 RPM |
|                       | temp1:       +46.0°C  |  +46°C   |
|                       | temp2:       +30.0°C  |  +30°C   |
|                       | temp3:      -128.0°C  | -128°C   |
|                       | intrusion0: OK        |          |
| GeForce GTX 7500 Ti   |                       |          |
|   temp                |                       | 39°C     |
|   graphics            |                       | 4%       |
|   video               |                       | 0%       |
|   memory              |                       | 5%       |
|   PCIe                |                       | 0%       |
|   fan rpm             |                       | 905RPM   |
|   fan level           |                       | 32%      |
| CPU usage             |                       | 21%      |
| free memory           |                       | 74%      |
| SAMSUNG SSD           |                       | 33°C     |

## Critique

- The graph is updated every 2 seconds, which is sufficient, but the x-axis and
  y-axis are not well labeled, as only the minimum and maximum values are
displayed.

- The power readings, such as `power1` from _lm-sensors_, are missing. Usually
  power readings are not critical. But sometimes they are. And to understand
why a system shuts down, it is sometimes important to monitor the power.

- The manufacturer-provided thresholds available in _lm-sensors_ are not
  propagated to _psensor_. And while it is easily possible to open _lm-sensors_
to configure _psensor_, it is a missed opportunity.  However, sometimes these
values provided by firmware engineers are incorrect, so the fact that _psensor_
does not automatically set these values is correct.

See example output from _lm-sensors_:

```bash
am15h_power-pci-00c4
Adapter: PCI adapter
power1:     49.02 W  (crit =  95.06 W)

k10temp-pci-00c3
Adapter: PCI adapter
temp1:      +28.4°C  (high = +70.0°C)
                       (crit = +90.0°C, hyst = +87.0°C)

it8721-isa-0290
Adapter: ISA adapter
in0:         2.80 V  (min =  +1.76 V, max =  +0.36 V)  ALARM
in1:         2.80 V  (min =  +0.38 V, max =  +1.80 V)  ALARM
in2:       828.00 mV (min =  +0.82 V, max =  +1.74 V)
+3.3V:       3.29 V  (min =  +1.99 V, max =  +0.10 V)  ALARM
in4:       792.00 mV (min =  +0.01 V, max =  +0.02 V)  ALARM
in5:         2.53 V  (min =  +2.23 V, max =  +0.82 V)  ALARM
in6:       204.00 mV (min =  +0.13 V, max =  +0.82 V)
3VSB:       72.00 mV (min =  +1.66 V, max =  +2.35 V)  ALARM
Vbat:        3.29 V
fan1:      1814 RPM  (min =  142 RPM)
fan2:       834 RPM  (min =   12 RPM)
fan3:         0 RPM  (min =   37 RPM)  ALARM
temp1:      +45.0°C  (low  = +64.0°C, high = +83.0°C)  sensor = thermistor
temp2:      +30.0°C  (low  = +120.0°C, high = +12.0°C) ALARM sensor = thermistor
temp3:     -128.0°C  (low  = -38.0°C, high = -119.0°C) sensor = disabled
intrusion0: OK
```

## Links

- Home page: <http://wpitchoune.net/psensor/>
- Source: <https://gitlab.com/jeanfi/psensor/tree/master>
- FAQ: <http://wpitchoune.net/psensor/faq.html>

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2024-02-11 | Add links                                            |
| 0.1.0   | 2024-02-10 | Initial release                                      |

