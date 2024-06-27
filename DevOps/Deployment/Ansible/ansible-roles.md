---
title: Ansible Roles
linkTitle: Ansible-Roles
author: Christian Külker
version: 0.1.1
date: 2024-06-27 
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
- ansible-playbook
discription: How to create a minimal Ansible role

---

## Introduction

This guide provides a detailed walkthrough for configuring an Apple Bluetooth
Keyboard on a Linux system using udev and Ansible. It aims to demonstrate the
creation and deployment of a custom Ansible role specifically tailored for
setting up and managing udev rules for hardware compatibility. Through a series
of structured steps, you will learn how to establish a role directory,
initialize and populate the role structure, and execute tasks within Ansible to
apply necessary configurations. The guide focuses on practical implementations,
including editing YAML files for task definitions and applying changes through
a playbook run on the target machine.

## Example Apple Bluetooth Keyboard Udev Configuration

1. Specify the location of the role directory. In case the default location is
not suitable, it is possible to define the location via the Ansible
configuration `role_path` or via the environment variable `ANSIBLE_ROLE_PATH`.
Setting this to `./rls` for example gets expanded to the current path plus
'rls' in Ansible.

    ```bash
    cd /tmp
    mkdir rls
    export ANSIBLE_ROLE_PATH="./rls"
    ansible-config dump|grep ROLE |grep PATH
    DEFAULT_ROLES_PATH(env: ANSIBLE_ROLES_PATH) = ['/tmp/rls']
    ```

2. Create a default structure for your role

    ```bash
    cd /tmp/rls
    ansible-galaxy role init apple-bluetooth-keyboard
    - Role apple-bluetooth-keyboard was created successfully
    tree
    └── apple-bluetooth-keyboard
        ├── defaults
        │   └── main.yml
        ├── files
        ├── handlers
        │   └── main.yml
        ├── meta
        │   └── main.yml
        ├── README.md
        ├── tasks
        │   └── main.yml
        ├── templates
        ├── tests
        │   ├── inventory
        │   └── test.yml
        └── vars
            └── main.yml
    
    10 directories, 8 files
    ```

3. Now edit the `README.md` and YAML files (even though they have the ending
`yml`) to add information, like author and license and specify for which
distribution this role is valid.

    - `README.md`
    - `meta/main.yml`

4. The main action happens in `tasks/main.yml`, `defaults/main.yml` and
sometimes in `vars/main.yml`.

    Edit `defaults/main.yml` (YAML markers like `---` are omitted)

    ```yaml
    # defaults file for apple-bluetooth-keyboard
    ns: apple-bluetooth-keyboard
    pfx: /opt
    repo_dst: "{{pfx}}/{{ns}}"
    repo_user: root
    repo_group: root
    repo_url: https://github.com/ckuelker/apple-bluetooth-keyboard.git
    rules: /etc/udev/rules.d/90-apple-bluetooth-keyboard.rules
    ```

    Edit `tasks/main.yml` (YAML markers like `---` are omitted)

    ```yaml
    # tasks file for apple-bluetooth-keyboard
    - name: "Create repository directory: {{repo_dst}}"
      file:
        path: "{{repo_dst}}"
        owner: "{{repo_user}}"
        group: "{{repo_group}}"
        mode: 0755
        state: directory
      # --- [ Apple bluetooth keyboard ] --------------------------------------
    - name: "Create repository directory: {{repo_dst}}"
      file:
        path: "{{repo_dst}}"
        owner: "{{repo_user}}"
        group: "{{repo_group}}"
        mode: 0755
        state: directory
    - name: "Clone repository {{repo_url}} to {{repo_dst}}"
      git:
        repo: "{{repo_url}}"
        dest: "{{repo_dst}}"
        clone: yes
        update: yes
      become: yes
      become_user: "{{repo_user}}"
    - name: Define udev rules for Apple bluetooth keyboard
      ansible.builtin.copy:
        src: "{{repo_dst}}{{rules}}"
        dest: "{{rules}}"
        owner: root
        group: root
        mode: '0644'
      register: udev_rule_file
      # --- [ udev reload ] ---------------------------------------------------
    - name: Reload udev rules
      ansible.builtin.command:
        cmd: udevadm control --reload-rules
      when: udev_rule_file.changed
    ```

5. Add a playbook "apple-bluetooth-keyboard.yaml" to your Ansible deployment
   and use the role (YAML markers like `---` are omitted)

    ```yaml
    - name: Configure Apple Keyboard and Udev
      hosts: role_client
      gather_facts: no
      become: yes
      tasks:
        - name: Define udev rules for disk names
          import_role:
            name: apple-bluetooth-keyboard
    ```

6. Run the playbook on `localhost`

    ```bash
    ansible-playbook  pb/apple-bluetooth-keyboard.yaml
    ```

Depending on your Ansible setup, other options might be needed.

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2024-06-27 | Change from vars to defaults; typo; formatting       |
| 0.1.0   | 2024-06-25 | Initial release                                      |

