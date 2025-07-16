---
title: Ansible Galaxy Usage
linkTitle: Ansible-Galaxy
author: Christian Külker
version: 0.1.0
date: 2025-07-16
type: doc
locale: en_US
lang: en
disclaimer: True
toc: True
categories:
- Ansible
commands:
- ansible-galaxy
tags:
- ansible-galaxy
discription: Usage of the ansible-galaxy command

---

## Understand the Ansible Distribution Package Content

Some guides suggest to install a "collection", for example from `github.com`,
like
[community.libvirt](https://github.com/ansible-collections/community.libvirt)
to manage `QEMU/KVM` machines. However Ansible already ships with some modules
prepackaged. To understand what community modules are included, use the
following command.

### Debian 12 Bookworm

```bash
ansible-galaxy collection list | grep community
community.aws                            10.0.0
community.ciscosmb                       1.0.10
community.crypto                         3.0.0-a2
community.digitalocean                   1.27.0
community.dns                            3.2.4
community.docker                         4.6.1
community.general                        10.7.0
community.grafana                        2.2.0
community.hashi_vault                    6.2.0
community.hrobot                         2.4.0
community.library_inventory_filtering_v1 1.1.1
community.libvirt                        1.4.0
community.mongodb                        1.7.10
community.mysql                          3.14.0
community.okd                            4.0.2
community.postgresql                     4.1.0
community.proxmox                        1.0.1
community.proxysql                       1.6.0
community.rabbitmq                       1.5.0
community.routeros                       3.7.0
community.sap_libs                       1.4.2
community.sops                           2.0.5
community.vmware                         5.7.0
community.windows                        3.0.0
community.zabbix                         4.0.0
```

## History

| Version | Date       | Notes                                               |
| ------- | ---------- | --------------------------------------------------- |
| 0.1.0   | 2025-07-16 | Initial release                                     |


