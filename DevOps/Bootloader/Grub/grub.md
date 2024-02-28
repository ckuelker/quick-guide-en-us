---
title: Deployment Grub
linkTitle: Grub
author: Christian Külker
date: 2023-04-24
version: 0.1.0
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Deployment
commands:
- grub-install
- update-grub
- update-initramfs
- modprobe
- mount
- ip
tags:
- Grub
- Chroot
- EFI
description: Issues around the deployment of grub

---

## Debian 11 Bullseye EFI mode

Sometimes it can happen that the network install in text mode finishes without
writing a boot loader to the disk on a system where EFI is enabled. I am not
sure why this happens. Usually the install asks for the location of the boot
loader to be written, but in such cases the install ends with a screen
suggesting to boot into the new system. And since grub is not installed, it
will never boot into the new system.

1. Boot into BIOS and make sure EFI is enabled
2. Make sure the boot device is the first one
3. If not sure disable network boot
4. Boot a Debian 11 __netinstall__ with graphical rescue mode; execute a shell
   in the root partition of the system to be installed.
5. If you want to do the work via ssh execute inside the `chroot`.
   - `mount devpts /dev/pts -t devpts`
   - `/etc/init.d/ssh start`
   - `ip addr show`
   - Log in to the system from remote
6. To understand which grub method (`x86_64-efi` or `i386-pc`) is used a simple
   `ls` will do:
   - `ls -1 /usr/lib/grub/`
   - `grub-mkconfig_lib`
   - `x86_64-efi`
   - `x86_64-efi-signed`
7. Install grub inside the master boot record (change `sda` to the correct
   device!)
   - `grub-install /dev/sda`
8. If you have the error I assume then you will get an error like (the No error
   reported must bean insider joke)
   - `Installing for x86_64-efi platform.`
   - `grub-install: warning: EFI variables are not supported on this system.`
   - `Installation finished. No error reported.`
9. Mount the `efivar` file system:
   - `modprobe efivarfs`
   - `mount -t efivarfs efivarfs /sys/firmware/efi/efivars`
10. Install grub, finally
   - `grub-install /dev/sda`
   - `Installing for x86_64-efi platform.`
   - `Installation finished. No error reported.`
11. Update the configuration
   - `update-grub`
12. Update `initramfs`
   - `update-initramfs -u`

There have been reports of this not working. As a workaround, switch the BIOS
to legacy mode, reboot, and then either reinstall the OS, or follow similar
steps as above, but omit `modprobe` and `mount`, and install the correct
`i386-pc` grub libraries via package before running `grub-install`.

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2023-04-24 | Initial release                                      |



