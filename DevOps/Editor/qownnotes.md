---
title: Qownnotes
author: Christian Külker
date: 2024-02-09
version: 0.1.1
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Editor
commands:
- qmake
- make
- ansible
tags:
- Qownnotes
- Ansible
description:
  Qownnotes is an editor to write markdown files and maintain a notes
  directory.

---

## Introduction

QOwnNotes is a note-taking application designed with a focus on user
independence and efficiency. It stands out by saving notes as plain-text
markdown files on the user's own computer, thereby avoiding the potential
issues of vendor lock-in seen in some other platforms. The application is
designed for quick performance and is conscious of system resource usage,
striving to keep its processor and memory usage low. QOwnNotes offers
customization and scripting options, allowing users to adjust it to their
workflow preferences. It includes additional functionalities such as real-time
markdown preview, integrated spellchecking, support for tabbed editing, and a
Vim mode, which enhance its practicality.

## Building on Debian 12

### Dependencies

```bash
aptitude install qtchooser libqt5websockets5 libqt5websockets5-dev \
libqt5x11extras5 libqt5x11extras5-dev
# OR (not tested)
# aptitude install qmake libqt6websockets6 libqt6websockets6-dev \
# libqt6x11extras6 libqt6x11extras6-dev qml6-module-qt5compat-graphicaleffects
```

### Source

```bash
git clone https://github.com/pbek/QOwnNotes.git -b release
cd QOwnNotes
git submodule update --init
cd src
```

### Build

```bash
# Build binary translation files if you want another language than English
# (not tested)
# lrelease QOwnNotes.pro

# Prepare build process
qmake
Project MESSAGE: Currently in RELEASE mode.

# Build the application
make
[...]
```

### Install

This will install __not__ into `/usr/local` but directly into `/usr`  if no
`PREFIX` variable is set via `qmake` and it might mess with packages!
Directories affected are: `/usr/{bin,share}`. It is also possible to run the
application directly from the `src` directory without installing it.
(`./QOwnNotes`)

```bash
cd src
make install
```

## Ansible Source Build Install

This Ansible cookbook will

- Install dependencies
- Create some directory structure
- Clone the repository
- Initialize the sub-modules inside the repository
- Configure QOwnNotes to be installed in /srv/qowennotes
- Build
- Install
- Make it accessible under zsh

It uses the function `path_add_after` to add the application to the current
path, this function should be available in your ZSH configuration `zshrc`
globally. Here it is for adding a new path _before_ and _after_.

```zsh
path_add_before(){
    if [ -d "$1" ] && [[ ":$PATH:" != *":$1:"* ]]; then
        path=($1 $path)
    fi
}
path_add_after(){
    if [ -d "$1" ] && [[ ":$PATH:" != *":$1:"* ]]; then
        path+=($1)
    fi
}
```

Then the application PATH is set for all _users_, made available for Ansible
inside the variable `users` like this:

```yaml
users:
- name: $USER
  group: $GROUP
  home: /home/$USER
```

Replace `$USER` and `$GROUP` with real values and add more entries like this to
the list in case you have more than one user.


```yaml
# qownnotes.yaml
# 0.1.0 2024-02-08 Initial release
---
- name: Install and setup QOwnNotes
  hosts: all
  become: yes  # This is required for installing packages and writing to /srv
  gather_facts: yes # Because of {{ansible_processor_vcpus}}
  vars:
    ns: qownnotes
    cores: "{{ansible_processor_vcpus}}"
    bin: QOwnNotes
    dst: "/srv/{{ns}}"
    src: "{{github.dir}}/pbek/{{bin}}/src"
    my_user: "{{user.user}}"
    my_group: "{{user.group}}"
    packages:
      - qtchooser
      - libqt5websockets5
      - libqt5websockets5-dev
      - libqt5x11extras5-dev
      - libqt5x11extras5
  tasks:
    - name: "{{ns}}: Install and update packages"
      package:
        name: "{{ packages }}"
        state: latest
    - name: "{{ns}}: Create directory for {{bin}} source"
      file:
        path: "{{github.dir}}/pbek"
        recurse: true
        owner: "{{my_user}}"
        group: "{{my_group}}"
        state: directory
        mode: '0755'
    - name: "{{ns}}: Clone '{{bin}}' repository to '{{github.dir}}/pbek/{{bin}}'"
      git:
        repo: 'https://github.com/pbek/{{bin}}.git'
        dest: '{{github.dir}}/pbek/{{bin}}'
        version: release
        force: yes # In case build was not successful
        clone: yes
        update: yes
      become: yes
      become_user: "{{my_user}}"
      when: not (github.dir + '/pbek/' + bin + '/.git') is directory
    - name: "{{ns}}: Initialize git submodules '{{github.dir}}/pbek/{{bin}}'"
      shell: git submodule update --init
      args:
        chdir: "{{github.dir}}/pbek/{{bin}}"
      become: yes
      become_user: "{{my_user}}"
      when: not (github.dir + '/pbek/' + bin + '/.git/modules') is directory
    - name: "{{ns}}: Configure {{bin}} prefix '{{dst}}'"
      shell: "qmake PREFIX={{dst}}"
      args:
        chdir: "{{src}}"
      become: yes
      become_user: "{{my_user}}"
      when: not (dst + '/bin/' + bin) is file
    - name: "{{ns}}: Build {{bin}} with {{core}} cores at '{{src}}'"
      shell: "make -j {{cores}}"
      args:
        chdir: "{{src}}"
      become: yes
      become_user: "{{my_user}}"
      when: not (dst + '/bin/' + bin) is file
    - name: "{{ns}} Install {{bin}} to '{{dst}}'"
      shell: make install
      args:
        chdir: "{{src}}"
      become: yes
      become_user: "{{my_user}}"
      when: not (dst + '/bin/' + bin) is file
    - name: "{{ns}}: Add dir '{{dst}}/bin' to PATH for each user"
      copy:
        content: "path_add_after {{dst}}/bin"
        dest: "{{item.home}}/.zsh/829_{{ns}}.zsh"
        owner: "{{item.name}}"
        group: "{{item.group}}"
        mode: 0644
      loop: "{{users}}"
      become: yes
```

## Debug

The application provides a debug tab inside the application settings.
Here some, but not all information.

### General Information

```yaml
**Current Date**: `Thu Feb 8 21:18:02 2024`
**Version**: `24.2.0`
**Build date**: `Feb  8 2024`
**Build number**: `1077`
**Platform**: `linux`
**Operating System**: `Debian GNU/Linux 12 (bookworm)`
**Build architecture**: `x86_64`
**Current architecture**: `x86_64`
**Release**: `Self-build`
**Qt Version (build)**: `5.15.8`
**Qt Version (runtime)**: `5.15.8`
**Portable mode**: `no`
**Settings path / key**: `$HOME/.config/PBE/QOwnNotes.conf`
**Application database path**: `$HOME/.local/share/PBE/QOwnNotes/QOwnNotes.sqlite`
**Application arguments**: `./QOwnNotes`
**Qt Debug**: `0`
**Locale (system)**: `en_US`
**Locale (interface)**: *empty*
**Primary screen resolution**: `1920x1200`
**Screen resolution(s)**: `1920x1200, 1200x1920, 1920x1200`
**Icon theme**: `mate`
**Notes in current note folder**: `2`
**Calendar items**: `0`
**Enabled scripts**: `0`
```

### Spellchecking

```yaml
**Enabled**: `true`
**Selected language**: `auto`
**Language codes**: `en_US`
**Language names**: `American English (United States)`
**Application dictionaries path**: `/$HOME/.local/share/PBE/QOwnNotes/dicts`
```

### Note folders

The note folder will be chosen at application startup.

```yaml
**currentNoteFolderId**: `1`
```

### Note folder `default`

```yaml
**id**: `1`
**isCurrent**: `yes`
**activeTagId**: `0`
**localPath**: `$HOME/Notes` # user choose this value
**remotePath**: `Notes`
**cloudConnectionId**: `1`
**isShowSubfolders**: `yes`
**isUseGit**: `no`
**allowDifferentNoteFileName**: `no`
**activeNoteSubFolder name**: *empty*
**database file**: `$HOME/Notes/notes.sqlite`
```

## Links

- Home: <https://www.qownnotes.org/>
- Source: <https://github.com/pbek/QOwnNotes>

## Videos

- A video about choosing a note taking application
  https://www.youtube.com/watch?v=XRpHIa-2XCE

## Critique

QOwnNotes is a versatile note-taking application designed for those who prefer
working with Markdown files. It offers a blend of simplicity and functionality,
catering to users who need a straightforward yet capable note-taking tool.

### Pros

- Uses plain Markdown files
- The markdown preview is interactive: Checking a TODO point also marks it as
  'done' in the source for example
- Easy compilation
- Git support (not tested)
- FOSS
- Encryption (not tested)
- Tags
- Web application support (not tested)

### Cons

- Install to `/usr` instead of `/usr/local` without PREFIX
- Uses many SQL lite databases (for meta data)
- Configuration data at several locations ($HOME/.local, $HOME/.config,
  $HOME/Notes)
- Some features (like versioning) only works with cloud service
- Binary is spelled mixed case including upper case

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2024-02-09 | Add debug section and Ansible playbook               |
| 0.1.0   | 2023-09-14 | Initial release                                      |

