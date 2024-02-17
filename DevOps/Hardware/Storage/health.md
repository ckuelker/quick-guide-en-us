---
title: Hardware Storage Health
linkTitle: Storage Health
author: Christian Külker
date: 2024-02-17
version: 0.1.3
type: doc
disclaimer: True
toc: True
categories:
- Health
- Monitoring
tags:
- Smart-Monitor-Tools
- LVM
- RAID
- NVMe
- USB
- UAS
commands:
- lsblk
- smartctl
- skdump
- hdparm
description: Using Self-Monitoring, Analysis and Reporting Technology System (SMART) to control and monitor storage systems

---

## Introduction

The health of a node's storage subsystem is important. There are many ways to
add storage to a node. The most common method has traditionally been a spinning
disc, a hard disk drive (HDD). In the last decade, memory-based storage
solutions have also been introduced: SSD or NVMe. While from a user's point of
view this all seems the same, from a system administrator's point of view,
managing storage subsystems requires different tools, skills and solutions.

When I had to install the first industrial-grade NVMe drive in 2017, I had to
write firmware to the device before it could be used. Today, NVMe drives come
with adequate firmware. But still, from a low-level protocol perspective, an
NVMe is different from an SSD, even though both use memory. Some tools can
handle the difference, others cannot. USB drives do not support SAT and
therefore cannot be queried via SMART directly, but sometimes indirectly.

| Tool     | SCSI | HDD (ATA/IDE/SAT) | SDD | NVMe |
| -------- | ---- | ----------------- | --- | ---- |
| skdump   | no   | yes               | yes | no   |
| smartctl |      | yes               | yes | yes  |

| Mode         | Linux Device |
| ------------ | ------------ |
| ATA          | /dev/hda     |
| IDE          | /dev/hda     |
| STA          | /dev/sda     |
| SCSI         | /dev/sda     |
| SCSI generic | /dev/sg0     |
| NVMe         | /dev/nvme0   |

From the common set of tables above, it can be seen that it is not possible to
deduce from the device name whether a device is supported or not.

## An Investigation with lsblk

First you need to understand the storage subsystem topology of the node you are
on. The `lsblk' command can be used to list block devices. This command
requires root privileges.

Here are three examples from three different nodes. The first node contains two
disks: `sda` and `sdb`. The second hard disk is partially encrypted and uses
`LVM'.

```bash
lsblk
NAME                MAJ:MIN RM   SIZE RO TYPE  MOUNTPOINT
sda                   8:0    0 931.5G  0 disk
├─sda1                8:1    0   499M  0 part
├─sda2                8:2    0   100M  0 part  /boot/efi
├─sda3                8:3    0   128M  0 part
├─sda4                8:4    0     1G  0 part
├─sda5                8:5    0 869.8G  0 part
└─sda6                8:6    0    60G  0 part
sdb                   8:16   0   477G  0 disk
├─sdb1                8:17   0   512M  0 part
├─sdb2                8:18   0   244M  0 part  /boot
└─sdb3                8:19   0 476.2G  0 part
  └─sdb3_crypt      253:0    0 476.2G  0 crypt
    ├─s1--vg-root   253:1    0 464.2G  0 lvm   /
    └─s1--vg-swap_1 253:2    0    12G  0 lvm   [SWAP]
```

The second node used two hard drives with software RAID.

``` bash
lsblk
NAME    MAJ:MIN RM   SIZE RO TYPE  MOUNTPOINT
sda       8:0    0 465.8G  0 disk
├─sda1    8:1    0  1000M  0 part
│ └─md0   9:0    0 999.4M  0 raid1 /boot
├─sda2    8:2    0   3.8G  0 part  [SWAP]
└─sda3    8:3    0   461G  0 part
  └─md1   9:1    0 460.9G  0 raid1 /
sdb       8:16   0 465.8G  0 disk
├─sdb1    8:17   0  1000M  0 part
│ └─md0   9:0    0 999.4M  0 raid1 /boot
├─sdb2    8:18   0   3.8G  0 part  [SWAP]
└─sdb3    8:19   0   461G  0 part
  └─md1   9:1    0 460.9G  0 raid1 /
```

The third node uses three NVMe drives in a software RAID with encrypted LVM
partitions.

```bash
lsblk
NAME                                   MAJ:MIN RM   SIZE RO TYPE  MOUNTPOINT
nvme0n1                                259:0    0 476.9G  0 disk
├─nvme0n1p1                            259:1    0     1G  0 part
│ └─md0                                  9:0    0  1022M  0 raid1 /boot
└─nvme0n1p2                            259:2    0 475.9G  0 part
  └─md1                                  9:1    0 475.8G  0 raid1
    └─luks-53152dbf-856e-4e13-b09f-... 253:0    0 475.8G  0 crypt
      ├─vg0-swap                       253:1    0    32G  0 lvm   [SWAP]
      └─vg0-root                       253:2    0 443.8G  0 lvm   /
nvme1n1                                259:3    0 476.9G  0 disk
├─nvme1n1p1                            259:4    0     1G  0 part
│ └─md0                                  9:0    0  1022M  0 raid1 /boot
└─nvme1n1p2                            259:5    0 475.9G  0 part
  └─md1                                  9:1    0 475.8G  0 raid1
    └─luks-53152dbf-856e-4e13-b09f-... 253:0    0 475.8G  0 crypt
      ├─vg0-swap                       253:1    0    32G  0 lvm   [SWAP]
      └─vg0-root                       253:2    0 443.8G  0 lvm   /
```

## A Simple Overview with skdump

- Home: <http://0pointer.de/blog/projects/being-smart.html>
- Repository: <https://git.0pointer.net/libatasmart.git/>
- Git: <git://git.0pointer.net/libatasmart.git>

The `skdump` tool has the advantage of giving a very simple but easy to
understand overview. The disadvantage is that this tool is limited to `ATA
SMART` storage subsystems, so `NVMe`'s are not supported.

### Installation

```bash
aptitude install libatasmart-bin
```

### Usage

If you are not interested in the details, or want to use the output of `skdump`
for cron jobs, you can just use the `--overall` switch.

Unfortunately, `skdump` does not have a man page. The help lists

~~~
Usage: skdump [PARAMETERS] DEVICE
Reads ATA SMART data from a device and parses it.

        --overall               Show overall status
        --status                Show SMART status
        --can-smart             Show whether SMART is supported
        --power-on              Print power on time in ms
        --power-cycle           Print number of power cycles
        --bad                   Print bad sector count
        --temperature           Print drive temperature in mKelvin
        --save[=FILENAME]       Save raw data to file/STDOUT
        --load[=FILENAME]       Read data from a file/STDIN instead of device
        -h | --help             Show this help
~~~

```bash
/usr/sbin/skdump --overall /dev/sda
BAD_SECTOR
/usr/sbin/skdump --overall /dev/sdb
GOOD

# In case you try a NVNe:
/usr/sbin/skdump --overall /dev/nvme0
Failed to open disk /dev/nvme0: No such device
/usr/sbin/skdump --can-smart /dev/nvme0
Failed to open disk /dev/nvme0: No such device

# In case you try to query a software RAID
/usr/sbin/skdump --can-smart /dev/md0
Failed to query whether SMART is available: Operation not supported

# In case you try to qery a USB disk (the result depends on the USB interface)
Device: sat12:/dev/sdb
Type: 12 Byte SCSI ATA SAT Passthru
Size: 1907729 MiB
Awake: Operation not supported
ATA SMART not supported.
```

Since the last commit to `skdump` was on 2012-05-21, it is not surprising that
this tool cannot support `NVMe`.

However for older devices the `--overall` paramter is very nice:

```bash
for i in a b c; do echo -n "DEVICE /dev/sd$i: ";skdump --overall /dev/sd$i;done
DEVICE /dev/sda: GOOD
DEVICE /dev/sdb: GOOD
DEVICE /dev/sdc: GOOD
```

## Smartmontools

The `smartmontools` package contains two utilities (`smartctl` and `smartd`)
and derived from the Linux `smartsuite` package. It supports ATA/SATA, SCSI/SAS
and NVMe disks, as well as SCSI/SAS tape devices.

* [Home]
* [SVN]
* [SVN RO Mirror]

### Installation

 ```bash
 # aptitude install smartmontools
 ```

Among others it installs

* `/usr/sbin/smartctl`
* `/usr/sbin/smartd`


### A Deep Dive with smartctl

Smartctl is part of the [Smart Monitor Tools] and has received severals updates over the years.

| Debian   | #  | `smartmontols`  |
| -------- | -- | --------------- |
| Bullseye | 12 | 7.3-1+b1        |
| Bullseye | 11 | 7.2-1           |
| Buster   | 10 | 6.6-1           |
| Stretch  |  9 | 6.5+svn4324-1   |

#### General Usage

In general, the `-a' command line option can be used to get a report. However,
this command line option does different things on different hardware. It will
print any SMART information about the hard disk, or TapeAlert information about
the tape drive or changer.

- For ATA equivalent to:  `-H -i -c -A -l error -l selftest -l selective`
- For SCSI equivalent to: `-H -i -A -l error -l selftest`
- For NVMe equivalent to: `-H -i -c -A -l error`

Select the drive. For example `/dev/sda`, `/dev/sdb`, `/dev/nvme0`, ...

```bash
smartctl -a /dev/sda > smartctl-report-dev-sda-2023-04-14.txt
```

#### Example Output

##### HDD

```bash
smartctl -i /dev/sda
smartctl 6.6 2016-05-31 r4324 [x86_64-linux-4.9.0-12-amd64] (local build)
Copyright (C) 2002-16, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF INFORMATION SECTION ===
Model Family:     Seagate Desktop SSHD
Device Model:     ST1000DX001-1CM162
Serial Number:    Z1DDK6Y6
LU WWN Device Id: 5 000c50 07b11bdcf
Firmware Version: CC43
User Capacity:    1,000,204,886,016 bytes [1.00 TB]
Sector Sizes:     512 bytes logical, 4096 bytes physical
Rotation Rate:    7200 rpm
Form Factor:      3.5 inches
Device is:        In smartctl database [for details use: -P show]
ATA Version is:   ACS-2, ACS-3 T13/2161-D revision 3b
SATA Version is:  SATA 3.1, 6.0 Gb/s (current: 6.0 Gb/s)
Local Time is:    Thu Jun 11 18:51:16 2020 CEST
SMART support is: Available - device has SMART capability.
SMART support is: Enabled
```

##### SSD

```bash
smartctl -i /dev/sdb
smartctl 6.6 2016-05-31 r4324 [x86_64-linux-4.9.0-12-amd64] (local build)
Copyright (C) 2002-16, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF INFORMATION SECTION ===
Device Model:     SAMSUNG MZ7TE512HMHP-00000
Serial Number:    AAAAAAAAAAAAAA
LU WWN Device Id: 5 002538 80001c92d
Firmware Version: EXT0100Q
User Capacity:    512,110,190,592 bytes [512 GB]
Sector Size:      512 bytes logical/physical
Rotation Rate:    Solid State Device
Device is:        Not in smartctl database [for details use: -P showall]
ATA Version is:   ACS-2, ATA8-ACS T13/1699-D revision 4c
SATA Version is:  SATA 3.1, 6.0 Gb/s (current: 3.0 Gb/s)
Local Time is:    Thu Jun 11 18:51:32 2020 CEST
SMART support is: Available - device has SMART capability.
SMART support is: Enabled

```

#### Health

The `smartctl` command has a `-H' or `--health' option that prints the health
status of a device.


```bash
smartctl -H /dev/sdb
smartctl 6.6 2016-05-31 r4324 [x86_64-linux-4.9.0-12-amd64] (local build)
Copyright (C) 2002-16, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF READ SMART DATA SECTION ===
SMART overall-health self-assessment test result: PASSED

smartctl -H /dev/sda
smartctl 6.6 2016-05-31 r4324 [x86_64-linux-4.9.0-12-amd64] (local build)
Copyright (C) 2002-16, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF READ SMART DATA SECTION ===
SMART overall-health self-assessment test result: PASSED
```

#### Test

`smartctl` tests can also be used to further investiaget the health of a
device.  Tests are specified with the `-t` or `--test` option followed by an
argument: offline, short, long, conveyance, select,N-M

    offline        SCSI  forground default test
    short          SCSI  background short self test
    long           SCSI  background long self test
    long           ATA   extended self test
    conveyance     ATA
    select,N-M     ATA

```bash
smartctl -t long  /dev/sda
smartctl 6.6 2016-05-31 r4324 [x86_64-linux-4.9.0-12-amd64] (local build)
Copyright (C) 2002-16, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF OFFLINE IMMEDIATE AND SELF-TEST SECTION ===
Sending command: "Execute SMART Extended self-test routine immediately in
off-line mode".
Drive command "Execute SMART Extended self-test routine immediately in off-line
mode" successful.
Testing has begun.
Please wait 115 minutes for test to complete.
Test will complete after Thu Jun 11 21:02:20 2020

Use smartctl -X to abort test.
smartctl  -l selftest /dev/sda
```

#### USB Disk

To use tools like `smartctl` with USB drives, you need to understand that a USB
drive, e.g. a SATA SSD or a spinning disc, is connected via an interface, and
that adds an abstraction layer in between. It depends on that interface __if__
and how well the SMART information can be red and changed. In some cases,
auto-detection may not work. Also keep in mind that `smartctl` has improved
over time. In one case, the Debian 9 Stretch was not able to handle SMART,
while the tool did on Debian 11 Bullseye.

TLDR:

- Use modern `smartctl`
- Scan all devices: `smartctl --scan`
- Try different `-d` option:
  - `smartctl -a -d ata -T permissive /dev/sdb`
  - `smartctl -a -d scsi -T permissive /dev/sdb`
  - `smartctl -a -d sat /dev/sdb`
- Use `--smart=on`

First scan the device to understand what type of device or interface we have:

Debian 9 Stretch (The device to be used, is the missing `/dev/sdb`)

```bash
smartctl --scan
/dev/sda -d scsi # /dev/sda, SCSI device
```

Debian 11 Bullseye (Also device `/dev/sdb`):

```bash
smartctl --scan
/dev/sda -d scsi # /dev/sda, SCSI device
/dev/sdb -d sat # /dev/sdb [SAT], ATA device
```

When we use ATA the device shows some information, but not all and it shows at
least one error:

```bash
smartctl -a -d ata --smart=on  /dev/sdb
smartctl 7.2 2020-12-30 r5155 [x86_64-linux-5.10.0-21-amd64] (local build)
Copyright (C) 2002-20, Bruce Allen, Christian Franke, www.smartmontools.org

Read Device Identity failed: Invalid argument

A mandatory SMART command failed: exiting. To continue, add one or more
'-T permissive' options.
```

Adding `-T permissive` gives more information, but this is still not good.

```bash
smartctl -a -d ata --smart=on -T permissive  /dev/sdb
smartctl 7.2 2020-12-30 r5155 [x86_64-linux-5.10.0-21-amd64] (local build)
Copyright (C) 2002-20, Bruce Allen, Christian Franke, www.smartmontools.org

Read Device Identity failed: Invalid argument

=== START OF INFORMATION SECTION ===
Device Model:     [No Information Found]
Serial Number:    [No Information Found]
Firmware Version: [No Information Found]
Device is:        Not in smartctl database [for details use: -P showall]
ATA Version is:   [No Information Found]
Local Time is:    Sun Apr 23 14:06:37 2023 CEST
SMART support is: Ambiguous - ATA IDENTIFY DEVICE words 82-83 don't show if
                  SMART supported.
SMART support is: Ambiguous - ATA IDENTIFY DEVICE words 85-87 don't show if
                  SMART is enabled.
A mandatory SMART command failed: exiting. To continue, add one or more
'-T permissive' options
```

Since the first guess using the `--scan' option is sub-optimal, we try SAT.
This works on Debian 11 Bullseye, but not on Debian 9 Stretch.

```bash
smartctl -a -d sat --smart=on /dev/sdb
smartctl 7.2 2020-12-30 r5155 [x86_64-linux-5.10.0-21-amd64] (local build)
Copyright (C) 2002-20, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF INFORMATION SECTION ===
Model Family:     Seagate Mobile HDD
Device Model:     ST2000LM007-1R8174
Serial Number:    ABCDEFGH
LU WWN Device Id: 5 000000 000000000
Firmware Version: SBK2
User Capacity:    2,000,398,934,016 bytes [2.00 TB]
Sector Sizes:     512 bytes logical, 4096 bytes physical
Rotation Rate:    5400 rpm
Form Factor:      2.5 inches
Device is:        In smartctl database [for details use: -P show]
ATA Version is:   ACS-3 T13/2161-D revision 3b
SATA Version is:  SATA 3.1, 6.0 Gb/s (current: 3.0 Gb/s)
Local Time is:    Sun Apr 23 14:08:24 2023 CEST
SMART support is: Available - device has SMART capability.
SMART support is: Enabled

=== START OF ENABLE/DISABLE COMMANDS SECTION ===
SMART Enabled.

=== START OF READ SMART DATA SECTION ===
SMART overall-health self-assessment test result: PASSED

General SMART Values:
Offline data collection status: (0x00) Offline data collection activity
                                       was never started.
                                       Auto Offline Data Collection: Disabled.
Self-test execution status:     (   0) The previous self-test routine completed
                                       without error or no self-test has ever
                                       been run.
Total time to complete Offline
data collection:              (    0) seconds.
Offline data collection
capabilities:                   (0x71) SMART execute Offline immediate.
                                       No Auto Offline data collection support.
                                       Suspend Offline collection upon new
                                       command.
                                       No Offline surface scan supported.
                                       Self-test supported.
                                       Conveyance Self-test supported.
                                       Selective Self-test supported.
SMART capabilities:           (0x0003) Saves SMART data before entering
                                       power-saving mode.
                                       Supports SMART auto save timer.
Error logging capability:       (0x01) Error logging supported.
                                       General Purpose Logging supported.
Short self-test routine
recommended polling time:       (   1) minutes.
Extended self-test routine
recommended polling time:        ( 332) minutes.
Conveyance self-test routine
recommended polling time:       (   2) minutes.
SCT capabilities:             (0x3035) SCT Status supported.
                                       SCT Feature Control supported.
                                       SCT Data Table supported.

SMART Attributes Data Structure revision number: 10
Vendor Specific SMART Attributes with Thresholds:
ID# ATTRIBUTE_NAME          FLAG   VAL WOR THR TYPE     UPDATE WHEN RAW_VALUE
  1 Raw_Read_Error_Rate     0x000f 081 064 006 Pre-fail Always  - 125167745
  3 Spin_Up_Time            0x0003 097 097 000 Pre-fail Always  - 0
  4 Start_Stop_Count        0x0032 100 100 020 Old_age  Always  - 37
  5 Reallocated_Sector_Ct   0x0033 100 100 036 Pre-fail Always  - 0
  7 Seek_Error_Rate         0x000f 068 060 045 Pre-fail Always  - 5940389
  9 Power_On_Hours          0x0032 100 100 000 Old_age  Always  - 385 (121 42 0)
 10 Spin_Retry_Count        0x0013 100 100 097 Pre-fail Always  - 0
 12 Power_Cycle_Count       0x0032 100 100 020 Old_age  Always  - 36
184 End-to-End_Error        0x0032 100 100 099 Old_age  Always  - 0
187 Reported_Uncorrect      0x0032 100 100 000 Old_age  Always  - 0
188 Command_Timeout         0x0032 100 100 000 Old_age  Always  - 0
189 High_Fly_Writes         0x003a 100 100 000 Old_age  Always  - 0
190 Airflow_Temperature_Cel 0x0022 071 045 040 Old_age  Always  - 29 (Min/Max 27/29)
191 G-Sense_Error_Rate      0x0032 100 100 000 Old_age  Always  - 0
192 Power-Off_Retract_Count 0x0032 100 100 000 Old_age  Always  - 4
193 Load_Cycle_Count        0x0032 100 100 000 Old_age  Always  - 1195
194 Temperature_Celsius     0x0022 029 055 000 Old_age  Always  - 29 (0 18 0 0 0)
197 Current_Pending_Sector  0x0012 100 100 000 Old_age  Always  - 0
198 Offline_Uncorrectable   0x0010 100 100 000 Old_age  Offline - 0
199 UDMA_CRC_Error_Count    0x003e 200 200 000 Old_age  Always  - 0
240 Head_Flying_Hours       0x0000 100 253 000 Old_age  Offline - 41 (80 206 0)
241 Total_LBAs_Written      0x0000 100 253 000 Old_age  Offline - 2284679980
242 Total_LBAs_Read         0x0000 100 253 000 Old_age  Offline - 37878002
254 Free_Fall_Sensor        0x0032 100 100 000 Old_age  Always  - 0

SMART Error Log Version: 1
No Errors Logged

SMART Self-test log structure revision number 1
No self-tests have been logged.  [To run self-tests, use: smartctl -t]

SMART Selective self-test log data structure revision number 1
 SPAN  MIN_LBA  MAX_LBA  CURRENT_TEST_STATUS
    1        0        0  Not_testing
    2        0        0  Not_testing
    3        0        0  Not_testing
    4        0        0  Not_testing
    5        0        0  Not_testing
Selective self-test flags (0x0):
  After scanning selected spans, do NOT read-scan remainder of disk.
If Selective self-test is pending on power-up, resume after 0 minute delay.
```

##### Trouble Shooting

The following has not been tested, but may be worth exploring.

<https://askubuntu.com/questions/637450/cannot-perform-smart-data-and-self-test-on-external-hard-drive>

1. Plug in device
2. `lsusb`, note id and vendor (Bus 002 Device 057: ID 0bc2:2323 Seagate RSS LLC Expansion+)
3. Unplug device
4. `modprobe -r uas`
5. `modprobe -r usb-storage`
6. `modprobe usb-storage quirks=VendorId:ProductId:u`
   Example: `modprobe usb-storage quirks=0bc2:2323:u`

   Alternatively:
   Example: `modprobe usb-storage quirks=0bc2:2323:`
7. Plug in device
8. See above link to make permanent

#### Understanding

The `smartctl' report obtained with the `-a' switch is divided into sections.
The names of the sections are not always the same.

~~~
smartctl 6.5+svn4324-1 sda
START OF INFORMATION SECTION
START OF READ SMART DATA SECTION

smartctl 6.6-1 sda
START OF INFORMATION SECTION
START OF READ SMART DATA SECTION

smartcl 7.2-1 nvme
START OF INFORMATION SECTION
START OF SMART DATA SECTION

smartctl 7.3-1+b1 sda
START OF INFORMATION SECTION
START OF READ SMART DATA SECTION
~~~

The tool `smartctl` gives a summary after the keyword `result:`. If you are
strictly only interested at the result a grep including the colon works.

```bash
smartctl -a /dev/sda |grep 'result:'
SMART overall-health self-assessment test result: PASSED
```

However warnings often includes the word 'result', therefore grep without
colons is advised:

```bash
smartctl -a /dev/sda |grep 'result'
SMART overall-health self-assessment test result: PASSED
Warning: This result is based on an Attribute check.
```

## `Smartctl` And `Skdump` Differs

If you compare the result of `smartctl` with `skdump`, it is obvious that the
booth tools have different opinions about the health of the disk. I have
noticed that when `skdump` gives a `BAD SECTOR` result, it is still `PASSED` by
`smartctl`. The error count is not a clear indicator. I have seen discs with a
low error count >0 reported as `GOOD` and `PASSED`, while a higher error count
is likely to be reported as `BAD SECTOR`.

So it seems difficult to decide when a drive is **bad**. However, a drive that
has 0 errors can be considered **good**.

## Hdparm

`hdparm` is not a classic tool for determining the health of a specific storage
subsystem device. However, it can be used to heuristically understand the
performance and therefore indirectly the health of a device. It is advisable to
query a device after purchase and decide on a speed margin for the future, and
see in subsequent queries if the device is performing as expected.

### Installing

```bash
aptitude install hdpam
```

### Information and Tests

```bash
hdparm -v /dev/sdb

/dev/sdb:
 multcount     =  0 (off)
 readonly      =  0 (off)
 readahead     = 256 (on)
 geometry      = 243201/255/63, sectors = 3907029167, start = 0
```

```bash
hdparm -t /dev/sdb

/dev/sdb:
 Timing buffered disk reads: 102 MB in  3.01 seconds =  33.93 MB/sec
```

```bash
hdparm -T /dev/sdb

/dev/sdb:
 Timing cached reads:   6416 MB in  2.00 seconds = 3211.52 MB/sec
```

```bash
hdparm -I /dev/sdb

/dev/sdb:

ATA device, with non-removable media
Standards:
        Likely used: 1
Configuration:
        Logical         max     current
        cylinders       0       0
        heads           0       0
        sectors/track   0       0
        --
        Logical/Physical Sector size:           512 bytes
        device size with M = 1024*1024:           0 MBytes
        device size with M = 1000*1000:           0 MBytes
        cache/buffer size  = unknown
Capabilities:
        IORDY not likely
        Cannot perform double-word IO
        R/W multiple sector transfer: not supported
        DMA: not supported
        PIO: pio0
```

```bash
smartctl -a /dev/sdb  -d scsi --smart=on
smartctl 7.2 2020-12-30 r5155 [x86_64-linux-5.10.0-21-amd64] (local build)
Copyright (C) 2002-20, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF INFORMATION SECTION ===
Vendor:               Seagate
Product:              Expansion+
Revision:             9300
Compliance:           SPC-4
User Capacity:        2,000,398,933,504 bytes [2.00 TB]
Logical block size:   512 bytes
Physical block size:  4096 bytes
Logical Unit id:      0x5000000000000001
Serial number:        NA8L4Z89
Device type:          disk
Local Time is:        Sun Apr 23 13:35:40 2023 CEST
SMART support is:     Available - device has SMART capability.
SMART support is:     Disabled
Temperature Warning:  Disabled or Not Supported

=== START OF ENABLE/DISABLE COMMANDS SECTION ===
Informational Exceptions (SMART) disabled
Temperature warning disabled

=== START OF READ SMART DATA SECTION ===
SMART Health Status: OK
Current Drive Temperature:     0 C
Drive Trip Temperature:        0 C

Error Counter logging not supported

Device does not support Self Test logging
```

## UAS

The uas (USB Attached SCSI) kernel module in Linux is responsible for handling
USB 3.0 storage devices that support the UAS protocol. This protocol is an
alternative to the older USB Mass Storage Bulk-Only Transport (BOT) standard
and is designed to improve the performance of USB storage devices.

__Understand if UAS ins active:__

```bash
lsmod|grep uas
uas                    32768  0
usb_storage            81920  1 uas
usbcore               331776  14 xhci_hcd,ehci_pci,snd_usb_audio,usbhid,\
                                 snd_usbmidi_lib,usblp,apple_mfi_fastcharge,\
                                 usb_storage,uvcvideo,ehci_hcd,ath3k,btusb,\
                                 xhci_pci,uas
scsi_mod              270336  6 sd_mod,usb_storage,uas,libata,sg,sr_mod
```

__Check the driver:__

```bash
lsusb -t|egrep -e 'usb-|uas'
        |__ Port 3: Dev 52, If 0, Class=Mass Storage, Driver=usb-storage, 480M
```

This device do not use `uas`, it uses `storage` so BOT. It should
display `uas` if UAS is used.

```bash
lsusb -t|egrep -e 'usb-|uas'
        |__ Port 2: Dev 5, If 0, Class=Mass Storage, Driver=uas, 5000M
```

To test BOT mode you can remove the UAS kernel module.

```bash
modprobe -r uas
modprobe -r usb-storage
```

- Use `smartctl` tests, `hdparm` tests or dd to stress the USB device
- Use `dmesg | grep -i usb` to check for any USB related errors.

## Conclusion

While `skdump` is easier to use and understand, the number of supported devices
is very limited and the software has not been updated since 2012, so unless the
system is old, it is recommended to use `smartctl` instead.

For many USB devices it is difficult to impossible to test the health of the
device. However, it may be useful to use UAS mode for the USB drive if
possible. If this is not possible, check for a quirk or switch to OBT USB mode.

While `hdparm` and similar tools do not directly check the health, they can be
used to evaluate a drive:

- performance
- power mode
- bad sectors

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.3   | 2024-02-17 | Skdump --overall                                     |
| 0.1.2   | 2024-02-16 | Reorder sections, + UAS section                      |
| 0.1.1   | 2023-03-14 | More on USB disks                                    |
| 0.1.0   | 2020-06-11 | Initial release                                      |

[Smart Monitor Tools]: http://www.smartmontools.org/
[Home]: https://www.smartmontools.org/
[SVN]: https://www.smartmontools.org/browser
[SVN RO Mirror]: https://github.com/smartmontools/smartmontools

