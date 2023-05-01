---
title: At
author: Christian Külker
date: 2023-05-01
version: 0.1.0
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Tools
commands:
- at
- tar
- mail
- wget
- gpg
- configure
- make
tags:
- At
Description:
    The 'at' command is a scheduling tool used to execute one-time tasks at a
    specified time in the future on Unix-based systems.

---

## Overview

The `at` command is a tool that allows you to schedule tasks to run at a later
time. It reads commands from standard input or a specified file and executes
the commands at a specified time. The `at` command is part of the `at` package
and is included in most Debian-based distributions.

## Debian Package Versions

| Distribution | Package Version |
|--------------|-----------------|
| Stretch      | 3.1.20-3        |
| Buster       | 3.1.23-1        |
| Bullseye     | 3.1.23-1.1      |

To install the `at` package, run:

```bash
aptitude update
aptitude install at
```
## Usage

The command is executed by the local user and the errors, if any, are displayed
in a mail to the local user. Or, if the command runs successfully, the output
(STDOUT) is sent to the local user via email.

### Example 1: Backup a directory

You can use the `at` command to schedule a one-time backup of a directory at a
specific time. To backup `/home/$USER/Documents` to `/home/$USER/Backup` at
22:00, you can run

```bash
echo "tar -czvf /home/$USER/Backup/Doc.tar.gz /home/$USER/Documents"|at 22:00
```

This will generate the following mail

~~~
Date: Mon, 01 May 2023 12:12:00 +0200
From: $USER <$USER@example.org>
To: $USER@host.example.org
Subject: Output from your job        4

tar: Removing leading `/' from member names
/home/$USER/Documents/
/home/$USER/Documents/Halmak.xml
/home/$USER/Documents/Halmak-course.xml
/home/$USER/Documents/Halmak-keyboard.xml
/home/$USER/Documents/Untitled 1.ods
/home/$USER/Documents/TODO.tdo
~~~

### Example 2: Send an email reminder

The `at` command can be used to send an email reminder at a specified time. To
send an email reminder to `user@example.com` at 3:30 pm, you can run:

```bash
echo "echo 'I reminded you!'|mail -s 'Reminder' user@example.com"|at 3:30 PM
```

This should work on all systems if the recipient is a local user. If the
recipient is a remote user, aka an external mail address, the mail client or
system must be configured to actually send to remote hosts using that mail
client and/or user account. If it is not configured that way, you will receive
an error message. In this case a local user `$USER@example.com` tried to send
mail to `$USER@example.org`.

~~~
Date: Mon, 01 May 2023 08:00:00 +0200
From: Mail Delivery System <Mailer-Daemon@host.example.com>
To: $USER@host.example.com
Subject: Mail delivery failed: returning message to sender

[-- Attachment #1 --]
[-- Type: text/plain, Encoding: 7bit, Size: 0.3K --]

This message was created automatically by mail delivery software.

A message that you sent could not be delivered to one or more of its
recipients. This is a permanent error. The following address(es) failed:

  $USER@example.org
    Mailing to remote domains not supported

[-- Attachment #2 --]
[-- Type: message/delivery-status, Encoding: 7bit, Size: 0.1K --]

Reporting-MTA: dns; s1

Action: failed
Final-Recipient: rfc822;$USER@example.org
Status: 5.0.0

[-- Attachment #3 --]
[-- Type: message/rfc822, Encoding: 7bit, Size: 0.5K --]

Date: Mon, 1 May 2023 08:00:00 +0200
From: "$USER ($USER@example.org)" <$USER@host.example.com>
To: $USER@example.org
Subject: Reminder
User-Agent: NeoMutt/20170113 (1.7.2)

I reminded you!

~~~

In some cases it makes more sense to use `mutt` instead of `mail`.

### Example 3: System update

You can use the `at` command to schedule a system update at a time when system
usage is low. To perform a system update at 2 a.m., you can run

```bash
echo "aptitude update" | at 2 AM
```

However, such a task is probably better implemented with cron.


### Example 4: Download a file at a specific time

You can use the `at` command to schedule a one-time task to download a file at
a specific time, which can be useful if you have limited bandwidth during peak
hours. To download a file from `https://example.com/file.tar` to
`/home/$USER/DL` tomorrow at 2:30 a.m., you can run

```bash
echo "wget -P /home/$USER/DL https://example.com/file.tar"|at 2:30 AM tomorrow
```

## Alternative Commands

1. Cron: The `cron` command is used to schedule recurring tasks. While `at` is
   useful for one-time tasks, `cron` is more suitable for tasks that need to be
   run repeatedly, such as regular backups or updates.

2. Anacron: Similar to `cron`, `anacron` is used to schedule recurring tasks.
   However, `anacron` is designed for systems that are not running 24/7, as it
   can execute missed tasks when the system is back online.

## Pros and Cons

### Pros

- Simple syntax for scheduling one-time tasks.
- Can schedule tasks based on a variety of time formats.
- Can read commands from a file or standard input.

### Cons

- Not suitable for scheduling recurring tasks.
- Lacks advanced features found in other scheduling tools like `cron` and 
  `anacron`.

## Compiling 'at' from Source

To compile `at` from source, follow these steps:

1. Install the required dependencies:

```bash
aptitude install build-essential automake autoconf gnupg flex sendmail
```

Perhaps `sendmail` can be replaced with another mail transfer agent or mail
user client that provides a `sendmail` command. Also, whether `autoconf` and
`automake` are really needed can be investigated. Some developer files are
created with these tools, but configuration and make work with and without
them, but the last line of `make` is different.

2. Download the source code and signature files:

```bash
wget http://software.calhariz.com/at/at_3.2.5.orig.tar.gz
wget http://software.calhariz.com/at/at_3.2.5.orig.tar.gz.sig
```

3. Verify the source code using the signature file:

```bash
gpg --verify at_3.2.5.orig.tar.gz.sig at_3.2.5.orig.tar.gz
```

This will usually fail. In this case, the key is not found on the local system,
and the error message looks like this:

```bash
gpg: Signature made Sun 27 Feb 2022 02:26:29 PM CET
gpg:                using RSA key 464BC7CD439FEE5E8B4098A0348A778D6885EF8F
gpg: Can't check signature: No public key
```

4. Import the GPG key used to sign the package

```bash
gpg --recv-keys 464BC7CD439FEE5E8B4098A0348A778D6885EF8F
```

Unfortunately, I could not find the key anywhere on this planet. So I skipped
this step.

5. If verification is successful on another planet, extract the source code and
   change to the extracted directory:

```bash
tar -xzvf at_3.2.5.orig.tar.gz
cd at-3.2.5
```

6. Compile and install:

As user:

```bash
./configure
make
```

As root

```bash
make install
```

Now you have successfully compiled and installed `at` from source.

## Useful Links

- Debian package 11 Bullseye <https://packages.debian.org/bullseye/at>
- Home page <http://blog.calhariz.com/>
- Source code releases <http://software.calhariz.com/at/>
- Source code repository <https://salsa.debian.org/debian/at>

## Documenation

```bash
man at
```

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2023-05-01 | Initial release                                      |

