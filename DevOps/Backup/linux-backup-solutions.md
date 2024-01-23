---
title: Linux Backup Solutions
author: Christian Külker
date: 2023-07-31
version: 0.1.4
type: doc
disclaimer: True
toc: True
categories:
- Backup
commands:
- tar
- cpio
- ssh
- dump
- dd
- rsync
- snapper
tags:
- Backup
description:
  General information regarding backups and overview of selected Linux backup
  software

---

A data backup is a copy of computer data taken and stored somewhere else
so that it may be used to restore the original after a data loss event.

Backups can be used to recover data after its loss on the original computer due
to deletion or corruption, or backups can be used to recover data from an
earlier time. The last case should be avoided and replaced with revision
control systems.

There are different kind of backups depending the data to be backed up. A
backup Of system data can be used to recover a system of well planned, called
disaster recovery. While a backup of user data can recover errors (deletion
for example) made by users.

No all backup systems can be uses as a disaster recovery. And sometimes the
ability depends on the complexity of the system to backed up: Computer clusters
and database server for example.

A backup system should only contain data considered worth saving and at least
one copy of it. Some systems are able to provide more than one copy or can
reconstruct data depending on a time stamp. They are suited for user data
recovery.

Data storage requirements of backup systems can be very large. Due this some
solutions overcome the price tag by offering expensive primary storage and less
expensive secondary storage which often differs in retrieval speed. Examples
are: SSD for primary storage and tapes for secondary storage.

Data to be stored needs to be selected. The selection can be up on time or
location or other criteria. The backup systems has to support certain methods
for different data types as: databases, open files, compression, encryption
and de-duplication.

## The 3-2-1 Rule

Every backup should be planned with a strategy in mind. The 3-2-1 rule states,
there should be 3 copies of the data, stored on 2 different media types (of 2
different computers) and 1 copy should not be off site.

The reason why one should use at least 2 different computers is, that sometimes
even hardware (not the media) might introduce errors to the backup. A defective
raid controller might not only crash the file systems of spinning hard disks
attach to it but also that of SSDs, for example.

## Methods

- Unstructured
- Full only/ System images
- Incremental
- Near-CDP
- Reverse incremental
- Differential

## Storage Media

- Magnetic tapes
- Hard disks (Spinning, SSD: via SCSI, USB, FireWire, eSATA, Ethernet, iSCSI or
  fibre channel)
- optical storage
- Flash memory (thumb drives, compactFlash, SmartMedia, memory sticks via USB)
- Network storage (via NFS, ssh, rsync)

## Data Selection

Usually the smallest entity to be backed up is a __file__. Files are organized
via a __file system__. The data types are: __static__ data (files, ...),
__live__ data (database, log file, ...) or __meta__ data (boot sector,
partition data, file modification data, ...).

For example, to back up the live data of a database it is often required to
shut down the database to make the database file static, before making a
backup.

## Custom Solutions

Every Linux is shipped with backup tools.

- cp   [files]
- tar  [files]
- dump [file systems] (archaic)
- dd   [block devices]
- cpio [files]

They can be used together with `crond` and/or `ssh` for example to create a
small and reliable backup system.

### NFS + Tar + Cron

For example, if you have mounted a directory of an external server via NFS
to `/remote/server/dir` you can put this script under `/etc/cron.daily` to
back up the `/etc` directory.

```bash
#!/usr/bin/bash

DIR=/remote/server/dir
FILE=$DIR/backup-etc-`date +'%F'`.tar.gz
if [ -d $DIR ]; then
  cd /
  if [ -f $FILE ]; then
      echo "ERROR: File $FILE exists"
      exit 2
  else
      /bin/tar czf $FILE etc
   fi
else
   echo "ERROR: No directory $DIR"
   exit 3
fi
```

This can of course __not__ be used to make a disaster recovery of the system,
but parts of the system configuration could be restored.

## File System Snapshots

If the target is to backup a partition (which might be questionable), so called
snapshots can be used for certain file systems. The following list should be
considered with caution as it do not claim that the mentioned file systems are
stable. Sometimes the can be done via file system specific `fsck` tools,
external commands or via an interim layer, like `LVM`.

- btrfs
- zfs
- lvm (ext3, xfs)

Software that can handle LVM:

- [Snapper and LVM thin-provisioned Snapshots](http://snapper.io/2012/07/25/lvm-thin-snapshots.html)

Software that sounds like a snapshot, but isn't:

- [rsnapshot] - uses `rsync` and hard links.  Debian-Edu (Skolelinux) use
  [rsnapshot] for backups in school environments.

## Backup Software

While it is advisable to create once own backup systems for young system
administrators to understand the underlying problems of backups, there are free
open source software already provided to Linux that can be used, if one
understand the core principles of backups.

| Package         | Debian   | Interface | Debian Buster       |  Bullseye   | Bookworm           |
| --------------- | -------- | --------- | ------------------- | ----------- | ------------------ |
| [amanda]-       |          | CLI       | 1:3.3.9-5           | 1:3.5.1-7   | 1:3.5.1-11+deb12u1 |
| [Areca Backup]  | n.a.     |           |                     |             |                    |
| [BackupPC]      |          | GUI       | 3.3.1-4             | 4.4.0-3     | 4.4.0-8            |
| [BackInTime]    |          | GUI       | 1.1.12-2            | 1.2.1-3     | 1.3.3-4            |
| [Bacula]        |          |           | 7.4.4+dfsg-6+deb9u2 | 9.6.7-3     | 9.6.7-7            |
| [Bareos]        | Buster   | GUI       | 16.2.4-3+deb9u2     | 1)          | n.a.               |
| [BorgBackup]    | YES      | CLI       |                     |             | 1.2.4-1            |
| [BorgBackup2]   | YES      | CLI       |                     |             | 2.0.0b5-1          |
|   [vorta]       | YES      | GUI       |                     |             | 0.8.10-1+deb12u1   |
| [Cpio]          |          | CLI       | 2.11+dfsg-6         | 2.13+dfsg-4 | 2.13+dfsg-7.1      |
| [dirvish]       |          | CLI       | 1.2.1-1.3           | 1.2.1-2.1   | 1.2.1-2.1          |
| [duplicity]     | Bullseye |           |                     | 0.8.17-1+b1 | 0.8.22-1+b3        |
| [rdiff-backup]  |          |           | 1.2.8-7             | 2.0.5-2     | 2.2.2-1            |
| [restic]        | YES      |           |                     |             | 0.14.0-1+b5        |
|  [npbackup]     | n.a.     |           |                     |             |                    |
| [rsbackup]      |          |           | 3.1-3+b1            | 6.0-2+b2    | 8.0-1              |
| [rsnapshot]     | Buster   | CLI       | 1.4.2-1             | 2)          | 1.4.5-1            |
| [slbackup]      |          | CLI       | 0.0.12-8            | 0.0.12-13   | 0.0.12-13          |
| [snapper]       |          | CLI       | 0.4.1-3             | 0.8.15-1    | 0.10.4-1           |
| [Warewulf]      | n.a.     | CLI       |                     |             |                    |
| Tar             |          | CLI       | 1.29b-1.1           | 1.34+dfsg-1 | 1.34+dfsg-1.2      |

1) Removed at 2021-12-01 due to maintainer [inactivity and 9 RC
bugs](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=1000906#5). See
also [tracker](https://tracker.debian.org/pkg/bareos)

2) Was removed at [2021-04-11](https://tracker.debian.org/pkg/rsnapshot)
due to bug [#986709](https://bugs.debian.org/986709) that tells that the
upstream maintainer do not intent continue to maintain it.

## Details On Some Software

### Amanda

- [amanda]
- Organization: full + incremental
- Storage: tape: tar, dump,

### Back In Time

- [BackInTime]
- Organization: snapshots
- Storage: disk
- Dependencies: rsync

### BackupPC

- [backuppc]
- Organization: full + incremental (hardlink shared between servers)
- Restore: tar, http, zip
- Remote: rsync, tar (over ssh/rsh/nfs) or ftp, samba, rsyncd
- Dependencies: Perl, Apache2

Even though the acronym PC is used, it seems that this tool can be used not
only for desktop or personal computers, but also for servers.

    + easy install (Debian Sqeeze, App + Webserver)
    + Webinterface easy to see

    - Webinterface has bugs (not choosing of host)
    - Webinterface no choice of target dir
    - Webinterface no creation of host cfg
    - Webinterface no comparison of files?

### BorgBackup

- Package borgbackup2
- GUI vorta <https://vorta.borgbase.com> <https://github.com/borgbase/vorta>
- GUI Pika Backup <https://github.com/pika-backup/pika-backup> 
+ Python3
+ Dedublication
+ Single file restore
+ Encryption
+ Compression

#### Documentation

- https://www.borgbackup.org/demo.html
- https://vorta.borgbase.com/
- https://vorta.borgbase.com/usage/restore/

### Dirvish

- [dirvish]
- Organization: rotating images (bank->vault->image)
- Restore: rsync, scp, cpio, tar, ...
- Storage: disk
- Dependencies: rsync, Perl, ...

### Duplicity

- [duplicity]
- Organization: full backup + incremental
- Restore: GnuPG, rdiff, and tar.
- Remote: scp/ssh, ftp, rsync, HSI, WebDAV, Tahoe-LAFS, and Amazon S3
- Storage: encrypted tar format
- Dependencies: Python, librsync, GnuPG,...

### Rsnapshot

- [rsnapshot]
- Organization: full + incremental (daily, weekly)
- Restore: cp
- Dependencies: Perl, rsync or librsync?

### Rdiff-backup

- [rdiff-backup]
- Organization: server/client full backup + incremental
- Remote: ssh
- Dependencies: librsync, phython

### Restic

+ Go? https://restic.net/ <https://github.com/rubiojr/awesome-restic>
+ Debian package
+ CLI
+ GUI via external projects
  - restic-browser (no restore) https://github.com/emuell/restic-browser
  - restatic (dead projectm for of vorta https://github.com/Mebus/restatic
  + npbackup (with prometheus support) https://github.com/netinvent/npbackup
  - resticguigx (beta) https://gitlab.com/stormking/resticguigx

### Slbackup

- [slbackup]
- Uses [rdiff-backup]
- Used by Skolelinux/ Debian-Edu

## Links

- [amanda]
- [areca backup]
- [areca backup wikipedia]
- [backuppc]
- [backuppc wikipedia]
- [BackInTime]
- [bacula]
- [bareos]
- [bareos home]
- [bareos open source]
- [bareos github]
- [bareos documentation]
- [cpio]
- [dirvish]
- [duplicity]
- [duplicity gitlab]
- [rdiff-backup]
- [rsnapshot]
- [rsbackup]
- [slbackup]
- [snapper]
- [warewulf]
- [warewulf wikipedia]

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.4   | 2023-07-31 | Bookworm; add borgbackup, restic                     |
| 0.1.3   | 2022-06-12 | Add reason of package removal from Bullseye          |
| 0.1.2   | 2022-06-11 | Shell -> bash, Bullseye                              |
| 0.1.1   | 2021-06-08 | Typos, Slbackup, Details, Dirvish, Duplicity         |
| 0.1.0   | 2021-06-07 | Initial release                                      |

[amanda]: http://www.amanda.org/
[areca backup]: http://www.areca-backup.org/
[areca backup wikipedia]: https://en.wikipedia.org/wiki/Areca_Backup
[backuppc]: https://backuppc.github.io/backuppc/
[backuppc wikipedia]: https://en.wikipedia.org/wiki/BackupPC
[BackInTime]: https://github.com/bit-team/backintime
[bacula]: http://www.bacula.org/
[bareos]: https://www.bareos.com/
[bareos home]: http://www.bareos.org/
[bareos open source]: https://www.bareos.com/community/github/
[bareos github]: https://github.com/bareos/
[bareos documentation]: https://docs.bareos.org/
[borgbackup]: https://borgbackup.github.io/borgbackup/
[borgbackup2]: https://borgbackup.github.io/borgbackup/
[vorta]: https://vorta.borgbase.com
[cpio]: http:///www.gnu.org/software/cpio/
[dirvish]: http://www.dirvish.org/
[duplicity]: http://duplicity.nongnu.org/
[duplicity gitlab]: https://gitlab.com/duplicity/duplicity
[npbackup]: https://github.com/netinvent/npbackup
[rdiff-backup]: http://rdiff-backup.nongnu.org/
[restic]: https://github.com/restic/restic
[rsnapshot]: http://www.rsnapshot.org/
[rsbackup]: http://www.greenend.org.uk/rjk/rsbackup/
[snapper]: http://snapper.io/
[slbackup]: https://salsa.debian.org/debian-edu-pkg-team/slbackup
[warewulf]: https://warewulf.lbl.gov/
[warewulf wikipedia]: https://en.wikipedia.org/wiki/Warewulf

