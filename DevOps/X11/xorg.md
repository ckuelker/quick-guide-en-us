---
title: Xorg
author: Christian Külker
date: 2023-04-27
version: 0.1.1
type: doc
disclaimer: True
toc: True
categories:
- X
tags:
- Xorg
commands:
- xrandr
description: Corner case configurations for Xorg

---

Normally, the configuration of X through Xorg is generated on the fly. Compared
to the old XFree86 setups of years ago, this works surprisingly well. There is
usually no need to create your own configuration. However, in some corner
cases, it is advantageous to understand the configuration, or even to create
part of the configuration explicitly.

## Using A Laptop With external Display Only (Internal LCD Closed)

Some laptops have a BIOS option to use only the external monitor. As long as
Linux does not detect the internal LCD, everything is fine. Some other BIOS's
may define the primary screen to be the external one, but usually Linux does
not care. And in some cases, there is no option.

In these cases, the onboard LCD screen is automatically configured via Xorg.
Whether this is good or bad depends on your window manager. In my experience,
Gnome or MATE will handle such a screen well, i.e. it will blank it and you can
log in, lock the screen and even log into the system correctly after
hibernation.

Some other window managers, such as XFCE4 on Debian 10 Buster, do not behave
well.  Some or all of the following can sometimes be observed:

- After the system boots, the login dialog will be displayed on the __closed__
  laptop screen and not on the external monitor
- After logging out and logging in again, the login dialog is displayed on a
  random screen, sometimes on the closed screen.
- After unlocking or logging in from the external monitor, the system gets
  aware of a closed internal screen, and if there is no activity (mouse for
  example), the system gets sent to hibernation and it seems that unlocking or
  logging in does not work. This happens even if the correct values for never
  hibernate when lid is closed have been selected in the power management setup
  dialog.

The following may fix some of the problems, but may not fix all of them. The
trick is to provide a part of the Xorg configuration that explicitly tells Xorg
not to guess the configuration for the onboard LCD, but to simply ignore it. As
a result, the screen is blanked out and unavailable to X configuration tools.
While this has advantages in a static scenario where the laptop is always
closed, it has the disadvantage that if you decide to use the laptop as a
mobile computer, no X will appear. The second step is to fix this unexpected
behavior.

First the following file `/usr/share/X11/xorg.conf.d/50-disable-lvds.conf` with
the content

```
Section "Monitor"
        Identifier "lvds monitor"
        Option "ignore" "true"
EndSection

Section "Device"
        Identifier "LVDS-1"
        Option "Monitor-LVDS-1" "lvds monitor"
EndSection
```

All but the `LVDS-1` value are fixed. To find out what value the device
identifier has, use the command `xrandr -q`. Usually on laptops there is a line
starting with or containing the string `LVDS` like this

```
LVDS-1 connected 1280x800+0+0 (normal left inverted right x axis y axis) 321mm x 213mm
```

Second, in case you forget how you configured X on your laptop. This might
happen if you decide to use the laptop on the road again (after 2 years of
using it as a desktop replacement), a hint on the login screen might help you
figure out why X did not start. Of course, you would have to restart the
laptop, or at least restart X.

```
export MOTD=/etc/motd
echo "">>$MOTD
echo "=================================================================">>$MOTD
echo "To start X on LCD screen, move or remove:">>$MOTD
echo "/usr/share/X11/xorg.conf.d/50-disable-lvds.conf">>$MOTD
echo "=================================================================">>$MOTD
```

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2023-04-27 | Improve writing, add history                         |
| 0.1.0   | 2021-03-22 | Initial release                                      |

 
