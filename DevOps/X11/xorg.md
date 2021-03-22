---
title: Xorg
author: Christian Külker
date: 2021-03-22
version: 0.1.0
type: doc
disclaimer: True
TOC: True
categories:
- X
- tags:
  - Xorg
- description: Xorg

---

Usually the configuration of X via Xorg is generated on the fly. Compared to
the old XFree86 setups years ago this works astonishing well. There is usually
no need to create ones own configuration. However in some corner cases there is
an advantage to understand the configuration or even create a part of the
configuration explicitly.

## Using A Laptop With external Display Only (Internal LCD Closed)

Some laptops have the BIOS option to only use the external monitor. As long as
Linux is not detecting the internal LCD screen all is fine. Some other BIOS'
might defined the primary screen to be the external one, but usually Linux do
not care about it. And in some cases there is no option.

In this cases the onboard LCD screen gets configured via Xorg automatically. If
this is a good or bad thing depends on the window manager. From my experience
Gnome or MATE will handle such a screen well, means will blank it and one can
log in, lock the screen and even after hibernation log into the system
correctly.

Some other window managers, as XFCE4 under Debian 10 Buster do not behave well.
Some or all of the following can be observed sometimes:

- After booting the system, the login dialog will be displayed on the closed
  laptop screen and not on the external monitor
- After logout and login again the login dialog will be displayed on a random
  screen, sometimes on the closed one.
- After unlocking or login from the external monitor, the system gets aware of
  a closed internal screen, and if there is no activity (mouse for example) the
  system get sent to hibernation and it seems that unlocking or login in  do
  not work.  This even happens if correct values of never sleeping when lid is
  closed was chosen in power management setup dialog.

The following might fix some of the problems, but maybe not all. The trick is
to provide a part of the Xorg configuration that explicitly tells Xorg to not
guess the configuration for the onboard LCD, but to just ignore it. As a result
the screen gets blanked and is not available for X configuration tools. While
this has pros in a static scenario where the laptop is always closed it has the
disadvantage that, if one decide to use the laptop as a mobile computer, no
X will show up. In a second step this unexpected behavior is addressed.

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

All except one value `LVDS-1` is fixed. To understand which value the
device identifier has, use the command `xrandr -q`. Usually on laptops
there is a line starting or containing the string `LVDS` like this:

```
LVDS-1 connected 1280x800+0+0 (normal left inverted right x axis y axis) 321mm x 213mm
```

Second, in case you forget how you configured X on your laptop. That can
happen if you decide to use the laptop (after it was used 2 years on your desk
as a desktop replacement) on the road again, a hint on the login screen might
help to figure out why X was not started. Of course you would need to restart
the laptop or at least X.

```
export MOTD=/etc/motd
echo "">>$MOTD
echo "=================================================================">>$MOTD
echo "To start X on LCD screen, move or remove:">>$MOTD
echo "/usr/share/X11/xorg.conf.d/50-disable-lvds.conf">>$MOTD
echo "=================================================================">>$MOTD
```
