---
title: Restore Mate Panel
linkTitle: Panel
author: Christian Külker
version: 0.1.0
date: 2025-07-14
type: doc
locale: en_US
lang: en
disclaimer: True
toc: True
categories:
- Mate
commands:
- dconf
tags:
- panel
discription: Restore Mate panel from backup

---

Restoring a desktop configuration from backup is easy on file based desktops
like `fvmw2` or similar. On Mate however the configuration is binary. This
document describes on how to restore the Mate panel configuration from backup.

```
Backup server: backup@example.org
Client: client@example.org with user $USER that has home $HOME
```

The Mate panel configuration lives under the URL `/org/mate/panel/` inside the
file `$HOME/.config/dconf/user`. So if you find this file on your backup server
there is a chance to restore the mate settings.

1. Create a file named `user2` with the content, for example at `~/user2`

  ```
  user-db:user2
  ```

2. Copy the old user database from the backup to the local client as `$USER`:

  ```
  scp root@backup.example.org/backup/$HOME/.config/dconf/user .config/dconf/user2
  ```

3. Dump backed up content (old) into a temporary text file `org-mate-panel.old`

  ```
  cd
  DCONF_PROFILE=$HOME/user2 dconf dump /org/mate/panel/ > org-mate-panel.old
  ```

4. Copy (Restore) the content of that file to the panel

  ```
  dconf load /org/mate/panel/ < org-mate-panel.old
  ```

The effect should be visible immediately. You can remove the files
`$HOME/.config/dconf/user2`, `user2` and `org-mate-panel.old`.


