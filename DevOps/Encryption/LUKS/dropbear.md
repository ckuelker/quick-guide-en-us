---
title: Dropbear
linkTitle: dropbear
author: Christian Külker
version: 0.1.1
date: 2026-06-12
type: doc
locale: en_US
lang: en
disclaimer: True
toc: True
categories:
- quick guide en us
commands:
- ssh
tags:
- dropbear
description: Dropbear SSH server

---


## Default Installation and Usage


```bash
aptitude update
aptitude install cryptsetup-initramfs dropbear-initramfs
cp /root/.ssh/authorized_keys /etc/dropbear/initramfs/authorized_keys
update-initramfs -u -v
```

## Advance Client Configuration

We assume server s0 and client c0. We can either add the c0 ssh user public key
directly to `/etc/dropbear/initramfs/authorized_keys` or to
`/root/.ssh/authorized_keys` and copy this file to
`/etc/dropbear/initramfs/authorized_keys` as described in the previous section.

Then add the following section to the user ssh configuration on c0
`~/.ssh/config`:

```
Host server-host.example.org-unlock
  Hostname e4.c8i.org
  Port 22
  User root
  RequestTTY yes
  RemoteCommand cryptroot-unlock
  #IdentityFile ~/.ssh/id_ed25519
```

## Advanced Server Configuration

It is possible to change the default values of Dropbear or the `initramfs`
IP handling. This might be needed in certain networks.

Debian 12 Bookworm                      | Debian 11 Bullseye
--------------------------------------- | ---------------------------------------
/etc/dropbear/initramfs/                | /etc/dropbear-initramfs/
/etc/dropbear/initramfs/dropbear.conf   | /etc/dropbear-initramfs/config
/etc/dropbear/initramfs/authorized_keys | /etc/dropbear-initramfs/authorized_keys

For example:

```bash
export CFG=/etc/dropbear/initramfs/dropbear.conf
echo 'DROPBEAR_OPTIONS="-I 180 -j -k -p 2222 -s -c cryptroot-unlock"' >> $CFG
update-initramfs -u -v
```

## Usage

### Update Access

```bash
# Add a key to dropbear
cat /home/USER/.ssh/id_ed25519.pub >> /etc/dropbear-initramfs/authorized_keys
# Update all kernel versions (-k all) and create a new ramfs (-c) 
update-initramfs -c -k all
# Alternatively update (-u) and be more verbose (-v)
update-initramfs -u -v
```

## Testing and Debugging

```bash
cryptsetup --verbose open --test-passphrase /dev/md1

cryptsetup luksDump /dev/md1|grep luks2
```

## Links

- <https://community.hetzner.com/tutorials/install-debian-11-with-lvm-encrypted-nvme-software-raid>
- <https://www.cyberciti.biz/security/how-to-unlock-luks-using-dropbear-ssh-keys-remotely-in-linux/>


## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
|   0.1.1 | 2026-06-12 | Usage section                                        |
|   0.1.0 | 2024-01-01 | Initial release                                      |


