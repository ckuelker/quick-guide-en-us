---
title: Dovecot Pigeonhole Sieve
author: Christian Külker
date: 2024-05-31
version: 0.1.2
type: doc
disclaimer: True
toc: True
categories:
- Mail
tags:
- Pigeonhole
- Sieve
- Dovecot
- Postfix
- MTA
- LMTP
- LDA
- IMAP
commands:
- sieve-test
- sievec
- lmtp
- doveconf
- postconf
description: Sieve is a programming language that can be used for email filtering

---

## Introduction

The IMAP server __Dovecot__ can be used to filter mail. The functionality is
called _Sieve_, a script language for mail filtering. For _Sieve_ to work, not
Postfix but Dovecot needs to be used to deliver mail to the mailbox or the mail
directory as a _local delivery agent_ (LDA) or via _local mail transfer
protocol_ (LMTP). If you do not know, whether you should use LDA or LMTP the
rule of thumb is: if you have a small mail server (preferably one user) use
LDA; if you have to serve many mails, users use LMTP. This means you have to
configure your _mail transfer agent_ (MTA), Postfix for example, to use Dovecot
to deliver mails and not itself.

There are at least two cons in terms of dovecot-lda in comparison to LMTP.

1. Since dovecot-lda is usually invoked with one user, error messages are less
   detailed. For example you cannot use Postfix `reject_unverified_recipient`
   with `pipe` and LDA.
2. Multi UID setups require workarounds, as MTAs usually do not run a LDA as
   root.

__A remark__: even though some configuration of LDA is done in a section
`protocol lda`, LDA is not a protocol. Thus `protocols = imap lda` throws an
error. While `protocols = imap lmtp` does not.

In case you are looking for a replacement of Thunderbird filters, you have to
know that Dovecot _Sieve_ filter rules are only active when new mail arrives
and can not be applied to sort the `INBOX` for already received mails.

## Postfix + Dovecot via LMTP

After it is clear whether to use LDA or LMTP, the MTA needs to be configured to
actually use Dovecot. In case of Postfix, one needs to know if only local
domains or also the virtual domains need to be configured.

The installation of _Sieve_ is straightforward.

```bash
aptitude install dovecot-sieve
```

This will create:

- `/etc/dovecot/conf.d/90-sieve.conf`
- `/etc/dovecot/conf.d/90-sieve-extprograms.conf`

### 90-sieve.conf

After installation this basically includes:

```conf
plugin {
  sieve = file:~/sieve;active=~/.dovecot.sieve
}
```

### 90-sieve-extprograms.conf

After installation this basically is:

```conf
plugin {
}
```

### Postfix + Dovecot LDA {untested}

If you are __not__ using LMTP, this is the way to install and use LDA. For LDA
__no__ additional package has to be installed.

For Postfix, local domains are configured usually in `main.cf`, while virtual
domains need configuration in `main.cf` and `master.cf`:

__main.cf:__

```cf
mailbox_command = /usr/lib/dovecot/dovecot-lda -f "$SENDER" -a "$RECIPIENT"
virtual_transport = dovecot
```

__master.cf:__

```cf
dovecot   unix  -       n       n       -       -       pipe
 flags=DRhu user=vmail:vmail argv=/usr/lib/dovecot/dovecot-lda -f ${sender}\
 -d ${recipient}
```

(See [Dovcot Wiki](https://wiki.dovecot.org/LDA/Postfix) for details.)

### Postfix + Dovecot LMTP

On the mail server install in addition to `dovecot-sieve` also `dovecot-lmtpd`.
The package `dovecot-lmtpd` basically contains 2 major files: `lmtp` and
`20-lmtp.conf`.

```bash
apt-file list dovecot-lmtpd
dovecot-lmtpd: /usr/lib/dovecot/lmtp
dovecot-lmtpd: /usr/share/bug/dovecot-lmtpd
dovecot-lmtpd: /usr/share/doc/dovecot-lmtpd/changelog.Debian.gz
dovecot-lmtpd: /usr/share/doc/dovecot-lmtpd/changelog.gz
dovecot-lmtpd: /usr/share/doc/dovecot-lmtpd/copyright
dovecot-lmtpd: /usr/share/dovecot/conf.d/20-lmtp.conf
```

The installation is just:

```bash
aptitude install dovecot-lmtpd
```

This will create the file `/etc/dovecot/conf.d/20-lmtp.conf` with an empty
protocol configuration. You have to add the _Sieve_ module to it. Best done in
`/etc/dovecot/local.conf`, like so.

```conf
protocol lmtp {
  mail_plugins = $mail_plugins sieve
}
```

There should be a service defined, so that Postfix can find it in its change
root environment.

```conf
service lmtp {
  unix_listener /var/spool/postfix/private/dovecot-lmtp {
    group = postfix
    mode = 0660
    user = postfix
  }
}
```

On systems other than Debian we might need to tell Dovecot to manage `lmtp`
like the configuration below. On Debian however this is done by the package,
that adds a file in `/usr/share/dovecot/protocols.d/` with the name
`lmtpd.protocol`. So the following entry is __not__ needed on Debian.

```conf
protocols = $protocols lmtp
```

The transfer protocol is easier to configure in Postfix. Usually `master.cf`
already contains the `lmtp` configuration line like:

```cf
lmtp      unix  -       -       y       -       -       lmtp
```

Then in `main.cf` only the transport via a local Unix socket needs to be
defined in `mailbox_transport`. If virtual domains are used then additionally
in `virtual_transport`.

```cf
mailbox_transport = lmtp:unix:private/dovecot-lmtp # for non virtual
virtual_transport = lmtp:unix:private/dovecot-lmtp # in case of virtual user
```

This is probably better than `lmtp:inet:localhost` over the network.

### Configuration

The configuration of _Sieve_ depends how and by whom _Sieve_ should be used. If
it is just one user, the admin for example, additions like `managesieved` are
not needed. Managed-sieve is a concept that allows _Sieve_ clients to add and
change filter rules on the server. This adds of course complexity and needs to
be configured properly to lower risks. For now this guide will __not__ explain
how to install and configure the package `dovecot-managesieved`.

The next thing to consider is, if the _manage sieve daemon_ is not used, how
many mail users the mail server has. If it has only one user, then a global
_Sieve_ file is OK. If it has more than one user, who wants to filter mails, an
individual file is appropriate. If there are more than one user or even many,
then `managesieve` would be better. An individual _Sieve_ script and a global
script with the directive `sieve_global` is mutual exclusive. However, there
are ways to add global scripts before or after individual script execution.
This is not discussed here.

For the sake of simplicity we will use the individual configuration for one
user. As we do not want filtering for system users, like root, postmaster,
abuse.

One major aspect of configuration is the storage location of the individual
_Sieve_ script. For this it is important to understand that the _mail
directory_ and the _home directory_ of a user can be identical, but also can be
different.  For some mail storage format, like `mbox`, which is just a file it
might be advantageous to store this into the home directory. In this case the
_mail directory_ and _home directory_ are identical and in addition to the
_mail directory,_ we have a file. Here of course the _Sieve_ script will be
saved into the _mail directory_ and the _home directory_ do not need to be
defined. On `Maildir` storage's the `Maildir` directory is a directory by it
self. And since some files can be stored in the _home directory_ via the OS
(like from `/etc/skel`), the OS might interfere with mail directory content, if
the _mail directory_ and the _home directory_ are identical. On this systems it
is advised to have different locations. However it is quite common to store the
`Maildir` directory inside the _home directory_. For this configuration we
assume the last case. The `/etc/dovecot/local.conf` should define:

```conf
# Linux User Home Directory (aka $HOME): _home directory_
mail_home = /home/%n
# Dovecot Maildir directory, called 'Maildir': _mail directory_
mail_location = maildir:/home/%n/Maildir
```

Be aware that the location of `%n` is taken from the `IMAP` password file. For
this the value of the 6th column need to be correct. Postfix ignores this
column. Therefore the value of the _home directory_ is configured in Postfix
and Dovecot separately and should match.

The location of a _Sieve_ directory or _Sieve_ script is defined on Debian in
`/etc/dovecot/conf.d/90-sieve.conf` as:

```conf
sieve = file:~/sieve;active=~/.dovecot.sieve
```

This means, that Dovecot `lmtp` will look first in the `$HOME/sieve`
__directory__ or if the directory do not exist it will use
`$HOME/.dovecot.sieve` as a script. If the file `$HOME/.dovecot.svbin` exists,
it will use the pre-compiled version. If the binary version is missing it will
create a binary version. To create the binary file yourself use `sievec`.

```bash
cd $HOME
sievec .dovecot.sieve
```

However the idea behind a __visible__ _Sieve_ directory in the home directory
is that `dovecot-manage-sieved` will manage this directory by adding scripts
and creating __one__ symlink from `.dovecot.sieve` to the active script inside
`~/sieve`. For example like `ln -s /home/user/sieve/last.sieve
/home/user/.dovecot.sieve`. So there can be only one active _Sieve_ script.

This is an example _Sieve_ script that might be used for testing.

```sieve
require ["fileinto"];

if header :matches "Subject" ["*SIEVE*"] {
    fileinto "Trash";
}
```

An e-mail, send from an external host, with the subject that includes "SIEVE"
should be moved to the `Trash` directory in the `IMAP` `INBOX`. Be aware that
the value of `fileinto` might depend on the `INBOX` structure of your mail
setup.

Successful execution looks like this in the Dovecot log file on Debian 10
(Buster) [some strings have been removed, like date, PID, 'Debug: sieve:']:

```log
lmtp(u@dom.tld): Pigeonhole version 0.5.4 () initializing
lmtp(u@dom.tld): include: sieve_global is not set; it is currently not possible
  to include `:global' scripts.
lmtp(u@dom.tld): file storage: Storage path `/home/u//sieve' not found
lmtp(u@dom.tld): file storage: Sieve storage path `/home/u//sieve' not
  found, but the active script `/home/u//.dovecot.sieve' is a regular file, so
  this is used for backwards compatibility.
lmtp(u@dom.tld): file storage: Using Sieve script path:
  /home/u/.dovecot.sieve
lmtp(u@dom.tld): file script: Opened script `.dovecot' from
  `/home/u/.dovecot.sieve'
lmtp(u@dom.tld): Using the following location for user's Sieve script:
  /home/u/.dovecot.sieve
lmtp(u@dom.tld): Opening script 1 of 1 from `/home/u/.dovecot.sieve'
lmtp(u@dom.tld): Loading script /home/u/.dovecot.sieve
lmtp(u@dom.tld): Script binary /home/u/.dovecot.svbin successfully
  loaded
lmtp(u@dom.tld): binary save: not saving binary /home/u/.dovecot.svbin,
  because it is already stored
lmtp(u@dom.tld): Executing script from `/home/u/.dovecot.svbin'
lmtp(u@dom.tld): Debug: Mailbox Trash: Mailbox opened because: lib-lda delivery
```

### Testing and Debugging

1. After installing and configuration, restart Dovecot and Postfix.
2. Make sure an external standard mail is delivered to the mail server
   that was changed
3. Look at the Postfix, Dovecot and `syslog` log files (add verbosity
   where needed) Are there errors?
4. Test if the example _Sieve_ script is OK (See below `sieve-test`)
   Is the script okay?
5. Send a mail that should trigger your _Sieve_ script. Is `lmtp`
   executed (see log files, usually Dovecot). Are there any errors?
   See above for an example without errors.
6. In case `lmtp` was executed. Was it executed without error? If
   not have a look in the _Sieve_ log (See below `.dovecot.sieve.log`).
7. Use `postconf` to check the Postfix configuration
8. Use `doveconf` to check the Dovecot configuration

### Debug Logging for Dovecot

To enable debug logging, add `mail_debug = yes` to `/etc/dovecot/local.conf`
and to change the locations, add the following:

```conf
mail_debug = yes
log_path = /var/log/dovecot.log
# If not set, use the value from log_path
info_log_path = /var/log/dovecot-info.log
# If not set, use the value from info_log_path
debug_log_path = /var/log/dovecot-debug.log
```

Restart Dovecot.

### sieve-test

After sending a mail that should trigger the _Sieve_ filter, do a grep on the
command line for that mail in your mail storage, so that the file name of the
mail can be understood.

__Example Test 018:__

```bash
cd $HOME
grep -r 'test 018' Maildir
Binary file Maildir/.Trash/dovecot.index.cache matches
Maildir/.Trash/cur/1673444162.M431092P29340.smtp,S=2668,W=2726:2,:Subject:\
 test 018 SIEVE
Maildir/.Trash/cur/1673444162.M431092P29340.smtp,S=2668,W=2726:2,:test 018
```
The file is `Maildir/.Trash/cur/1673444162.M431092P29340.smtp,S=2668,W=2726:2,`
and since it is in `.Trash` we understand that the _Sieve_ script worked.

__Example Test 016:__

This mail was send before Sieve was active.

```bash
grep -r 'test 016' Maildir
Binary file Maildir/dovecot.index.cache matches
Maildir/cur/1673443420.M696259P28433.smtp,S=2710,W=2768:2,S:Subject: test 016\
 SIEVE
Maildir/cur/1673443420.M696259P28433.smtp,S=2710,W=2768:2,S:test 016
```

The mail for test 016 is in the `INBOX`  and was delivered when `lmtp` was not
configured or the _Sieve_ script was not found at that time. We can test if it
would have been correctly delivered if the Dovecot configuration was perfect.
We execute it as user root and tell that we would like to do it on behalf of
user `u`.

```bash
sieve-test -u u .dovecot.sieve
  Maildir/cur/1673443420.M696259P28433.smtp,S=2710,W=2768:2,S
sieve-test(u)<1118><>: Debug: auth USER input: u@dom.tld local_port=0 uid=1000
  gid=1000 home=/home/u/
sieve-test(u)<1118><>: Debug: changed username to u@dom.tld
sieve-test(u)<1118><>: Debug: Added userdb setting: plugin/local_port=0
sieve-test(root): Debug: Effective uid=1000, gid=1000, home=/home/u/
sieve-test(root): Debug: Namespace inbox: type=private, prefix=, sep=,
  inbox=yes, hidden=no, list=yes, subscriptions=yes
  location=maildir:/home/u/Maildir
sieve-test(root): Debug: maildir++: root=/home/u/Maildir, index=, indexpvt=,
  control=, inbox=/home/u/Maildir, alt=

Performed actions:

 * store message in folder: Trash

Implicit keep:

  (none)

sieve-test(root): Info: final result: success
```

This will test only and not move.

### .dovecot.sieve.log

If you can see that the `lmtp` is executed, but an error is indicated, look
into `$HOME/.dovecot.sieve.log` for the error message.

## Pigeonhole

Is [Pigeonhole](https://pigeonhole.dovecot.org/) the same as _Sieve_? No,
pigeonhole is the project and _Sieve_ is the mail filter language. The
Pigeonhole project develops dovecot-sieve and `dovecot-managesieved`. It is
possible to just use dovecot-sieve alone. In this case the user needs to create
and edit a _Sieve_ script on the mail server. With `dovecot-managesieved,` it
is possible that the user supply a script remotely (via telnet or Thunderbird
plugin).

## RFC

There are many ways to filter e-mails. One way is to filter mail on the mail
server with the IMAP server Dovecot by using the tools of the pigeonhole
project. The project implements the mail filter language called _Sieve_
([RFC5228] and updates [RFC5229], [RFC5429], [RFC6785], [RFC9042] ) so that
Dovecot can filter mail: for example move a mail from the IMAP INBOX folder to
a different folder.

[RFC5228]: https://datatracker.ietf.org/doc/html/rfc5228.html
[RFC5229]: https://www.rfc-editor.org/rfc/rfc5229
[RFC5429]: https://www.rfc-editor.org/rfc/rfc5429
[RFC6785]: https://www.rfc-editor.org/rfc/rfc6785
[RFC9042]: https://www.rfc-editor.org/rfc/rfc9042

[rfc3028](http://tools.ietf.org/html/rfc3028) (is obsolete, now RFC5228)
Obsoleted by: 5228, 5429

## Sieve Scripts

_Sieve_ scripts are easy to understand. See
[Wikipedia](https://de.wikipedia.org/wiki/Sieve) for example.  With
`manageesieve` and the Thunderbird extension this scripts can be managed on the
client.

```
require "fileinto";
if header :contains "X-Spam-Flag" "YES" {
  fileinto "Junk";
}
```

## Debian Packages

- `dovecot-lmtpd`: Locale mail transfer protocol daemon
- `dovecot-managesieved`: Dovecot ManageSieve server
- `dovecot-sieve`: Sieve filters support for Dovecot

## Further Read

- [Sieve clients](http://sieve.info/clients)

## Glossary

- __DB__: Database
- __DIR__: Directory
- __DOM__: Domain
- __GID__: Group ID
- __ID__: Identification
- __LDA__: Local delivery agent
- __LMTP__: Local mail transfer protocol
- __MTA__: Mail transfer agent
- __PVT__: Private
- __TLD__: Top level domain
- __UID__: User ID

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2024-05-31 | Initial release for Quick Guide                      |
| 0.1.1   | 2023-01-11 | Install Dovecot _Sieve_ + lmtpd with Postfix         |
| 0.1.0   | 2020-11-21 | Initial release                                      |

