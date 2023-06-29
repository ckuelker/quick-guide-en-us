---
title: Ansible Local Mode
linkTitle: Ansible-Local-Mode
author: Christian Külker
date: 2023-06-29
version: 0.1.0
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Ansible
commands:
- ansible-playbook
tags:
- Ansible-Local-Mode
description:
    The Ansible Local Mode installs software on a local machine from a local
    playbook without a Ansible server

---

## Abstract

Ansible is primarily an agentless system that typically pushes configuration
from a controlling node (Ansible server). However, Ansible could be installed
on each client node and run Ansible playbooks from a Git repository via cron.
This creates a resilient system that installs software without a single point
of failure because no central server is required. The central git repository is
cloned to each client, so there is no single point of failure. This is not
ideal, of course, as it requires special care in managing secrets (such as SSH
keys or API keys). And this also has some challenges when some client nodes
depend on others (orchestration). In clouds, automatic recovery may also be
difficult with this setup, but if you are managing real machines, you would
have to do it differently anyway, for example by using a bare metal deployment
system like FAI, warewulf or others. So this last point is not really a
criticism of local mode.

## Installation

This installation was tested on Debian 12 bookworm.

Install Ansible

```bash
aptitude install ansible
```

This will install:

~~~
ansible ansible-core ieee-data libyaml-0-2 python-babel-localedata
python3-anyio python3-argcomplete python3-babel python3-cffi-backend
python3-click python3-colorama python3-cryptography python3-dnspython
python3-h11 python3-h2 python3-hpack python3-httpcore python3-httpx
python3-hyperframe python3-jinja2 python3-jmespath python3-kerberos
python3-libcloud python3-lockfile python3-markdown-it python3-markupsafe
python3-mdurl python3-netaddr python3-ntlm-auth python3-pygments
python3-requests-kerberos python3-requests-ntlm python3-requests-toolbelt
python3-resolvelib python3-rfc3986 python3-rich python3-selinux
python3-simplejson python3-sniffio python3-tz python3-winrm python3-xmltodict
python3-yaml
~~~

## Configuration

1. Create or clone a git repository in `/srv/pb`

```bash
cd /srv
git clone URL:my-ansible-local-mode.git pb
```

2. Create a sample configuration or use one from your
   `my-ansible-local-mode.git`. We assume that
   this will create the file `screen.yml` in `/srv/pb`.

```yaml
---
- hosts: localhost
  gather_facts: no
  tasks:
    - name: Install screen
      apt:
        name: screen
        state: present
      become: yes
```

## Run

Run Ansible with `ansible-playbook`

```bash
ansible-playbook screen.yml
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that
the implicit localhost does not match 'all'

PLAY [localhost] **************************************************************

TASK [Install screen] *********************************************************
changed: [localhost]

PLAY RECAP ********************************************************************
localhost : ok=1 changed=1 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0
```

## Check

Check the result

```bash
dpkg -l |grep 'ii  screen '
ii screen 4.9.0-4 amd64 terminal multiplexer with VT100/ANSI terminal emulation
```

## Automate

Setup a cron job

In this example we assume that the git repository is at
`/srv/pb` and it contains the file `screen.yml`.

```cron
0 * * * * cd /srv/pb&& /usr/bin/git pull&& /usr/bin/ansible-playbook screen.yml
```

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2023-06-29 | Initial release                                      |
 

