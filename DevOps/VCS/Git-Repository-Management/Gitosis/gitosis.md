---
title: Gitosis
author: Christian Külker
date: 2024-02-28
version: 0.1.1
type: doc
disclaimer: True
toc: True
categories:
- VCS
- Git-Repository-Management
tags:
- Gitosis
description: VCS Managemant with Gitosis

---

## Introduction

This old document from 2011 describes __gitosis__, software to manage and
deploy git repositories. It provides an easy and stable model to access git
repositories for teams using a shared server. As of Debian 7.x (Wheezy),
gitosis has been replaced by [Gitolite](../Gitolite/gitolite.md). Therefore,
this document will most likely not receive substantial updates and is for
historical purpose only.

## Manual Installation [0.1.0]

It is relatively easy to manually install __gitosis__. This installation will
install it to `/home/srv` (user data) to `/srv` (short path).  It uses two
computers: (a) server and (b) client.

```bash
# On the server as root:
rmdir /srv
mkdir /home/srv
ln -s /home/srv /srv

aptitude install gitosis
```

Make sure this user exists

```bash
id gitosis
uid=104(gitosis) gid=107(gitosis) groups=107(gitosis)
```

The most important locations are:

```bash
/home/srv/gitosis
/home/srv/gitosis/git
/usr/bin/gitosis-init
/usr/bin/gitosis-run-hook
/usr/bin/gitosis-serve
```

For the initial setup, we need one SSH key. You can use the
key of your current user on the __client__.

```bash
# on the client
cd
scp .ssh/id_rsa.pub rep:/tmp
```

You need to be `root` first, and then change to the user `gitosis`

```bash
# As root on the server:
mkdir /srv/gitosis   # sometimes
su - gitosis
gitosis-init < /tmp/id_rsa.pub
Initialized empty Git repository in /home/srv/gitosis/repositories/gitosis-admin.git/
Reinitialized existing Git repository in /home/srv/gitosis/repositories/gitosis-admin.git/
```

On the client

```bash
git clone gitosis@rep.example.com:gitosis-admin.git
```

Then, you edit the _gitosis-admin_ repository on your client.

## Creating a New Repository [0.1.0]

This will create the repository `vwtg` on `m.example.com`.

```bash
export REP=vwtg
mkdir $REP
cd $REP
git init
echo "gitosis@m.example.com:$REP.git" >| REP
git add REP
git commit -m init REP
git remote add origin gitosis@m.example.com:$REP.git
git config branch.master.remote origin
git config branch.master.merge refs/heads/master
git push origin master:refs/heads/master
git pull
```

### Adding Access For A New Client

The access to __gitosis__ is based on _public_ SSH keys.

1. Add pub key to _keydir_
2. Name it after the login and machine name, usually domain name, for
   example `user@machine2.pub`
3. Add that string `user@machine2` to the 'members' section like so:

    members = user@machine1 user@machine2

### Configuring and Using the Git Repository

This example creates the repository `hpc-benchmark-suite` and
assign it to the group `hpc-sw-dev` inside the configuration in
the first step and then in the second step it actually created
the repository. In a third step it adds configuration to
the repository.

1. Add configuration to `gitosis.conf`

  ~~~
    [repro hpc-benchmark-suite]
    description = Aurora Benchmark Suite
    owner = user_id

    [group hpc-sw-dev]
    writable = hpc-benchmark-suite
    members = user_id
  ~~~

2. Create locally a git repository

  ```bash
    mkdir hpc-benchmark-suite
    cd hpc-benchmark-suite
    git init
    touch README
    git add README
    git commit -m init README
  ```

3. Add a remote origin

  ```bash
    git remote add origin gitosis@rep.example.com:hpc-benchmark-suite.git
    #git remote add origin gitosis@m.example.com:nagios.git
  ```

Or

  ```bash
    git remote add origin gitosis@m:hpc-benchmark-suite.git
  ```

If you already have a remote origin, edit `.git/config`

Then push something

```bash
    git push origin master:refs/heads/master
```

To pull we need to update the configuration

```bash
    git config branch.master.remote origin
    git config branch.master.merge refs/heads/master
```

## Global Access [0.1.0]

In addition to the usual restricted access, it is possible
to give access to a given repository to _all_.

```bash
# As root or gitosis user (depending on access rights)
cd /srv/gitosis/repositories/hpc-benchmark-suite.git
touch git-daemon-export-ok
/usr/lib/git-core/git-daemon --base-path=/srv/gitosis/repositories/
```

Global access is then possible for some directories as:

```bash
git clone rep.example.com:hpc-benchmark-suite.git
```

However it is still possible to access it as a specific user.

```bash
git clone gitosis@rep.example.com:hpc-benchmark-suite.git
```

## Access to GitWeb [0.1.0]

Installation

```bash
aptitude install gitweb
```

Edit `/etc/gitweb.conf`

```bash
sed -ie 's%/var/cache/git%/srv/gitosis/repositories%' /etc/gitweb.conf
echo '$export_ok = "git-daemon-export-ok";' >> /etc/gitweb.conf
```

~~~
    [gitosis]
    gitweb = yes
~~~

Then go to the directory of the project you want to be visible in gitWeb

```bash
cd /srv/gitosis/repositories/hpc-benchmark-suite.git
touch git-daemon-export-ok
```

Finally start the GitWeb daemon as __gitosis__ user

```bash
/usr/lib/git-core/git-daemon --base-path=/srv/gitosis/repositories/
```

Go to the URL and verify: <http://rep/gitWeb/>

There is a known problem, that the started daemon vanishes from the process
list and/or the file
`/srv/gitosis/repositories/<REPO>.git/git-daemon-export-ok` gets removed (and
the daemon stops).

To mitigate this a `daemon = yes` and `gitWeb = yes` line has to added to
`gitosis.conf` for that repository.

~~~
daemon = yes
gitweb = yes
~~~

## Rename a gitosis Repository [0.1.0]

The rename a `gitosis` repository the order of actions is important.

Before

    [group main]
    writable = nagios

After

    [group main]
    writable = nagios3

Push changes

```bash
git push origin master
```

Connect to the `gitosis` server and rename the correct folder

```bash
cd /home/srv/gitosis/repositories
mv nagios nagios3
```

Change the remote reference in all repository clones

```bash
cd /srv
mv nagios nagios3
cd nagios3
git remote rm origin
git remote add origin gitosis@m:nagios3.git
git config branch.master.remote origin
git config branch.master.merge refs/heads/master
```

## Remove a Repository [0.1.0]

The order of actions to remove an existing repository is important.  This
example uses the git repository `sai` and changes need to be done in
`gitosis.conf`. Remove all `sai` entries, as `gitosis admin` user, and then as
root.

As the last step remove the repository.

```bash
cd /srv/gitosis/repositories
rm -rf sai.git
```

## Use A Git Hook [0.1.0]

To execute an action for every change on the server, add a
git hook inside the repository.


```bash
cd /srv/gitosis/repositories/christian.git/hooks
vim post-receive
```

```bash
#!/bin/bash
unset $(git rev-parse --local-env-vars)
DATE=`date +'%FT%T'`
echo $DATE >/tmp/git-hook.log
```

## Other Links

- <https://wiki.archlinux.org/index.php/Gitosis>

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2024-02-28 | Release to GitHub, update Markdown formatting        |
| 0.1.0   | 2011-07-14 | Initial release                                      |






