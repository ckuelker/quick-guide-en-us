---
title: Grc
author: Christian Külker
date: 2023-05-12
version: 0.1.3
type: doc
disclaimer: True
toc: True
categories:
- Tools
tags:
- Grc
commands:
- tail
- grc
- perl
- tranceroute
- gcc
- ping
- netstat
- tail
- ps
description: Make logfiles and ouput colorful

---

The Python tool [grc] is very useful for finding problems in log files or other
output. `grc` stands for _Generic Colouriser_ and is a Linux command line
utility that enhances the output of other commands by adding colour to make it
more readable and visually appealing. It is particularly useful for visually
distinguishing between different types of information in long or complex
output. This short tutorial will guide you through the process of installing
and using grc.

## Debian Versions

- The Debian 9.13 Stretch version is `1.9-1`
- The Debian 10 Buster version is    `1.11.3-1`
- The Debian 11 Bullseye version is  `1.11.3-1.1`
- It is developed on [github] under the GPLv2.

## Installation

```bash
aptitude install grc
```

## Basic Usage

### Method 1: Directly with `grc`

The simplest:

```bash
grc COMMAND
grc netstat
grc ping hostname
grc tail /var/log/syslog
grc ps aux
```

Simple:

```bash
grc tail -f /var/log/apache2/error.log
```

Advanced:

Without [grc]:

```bash
tail -f myfile.log | perl -pe 's/SEVERE/\e[1;31m$&\e[0m/g'
```

And with [grc] for the first time:

```bash
mkdir ~/.grc
echo "regexp=SERVERE" > ~/.grc/myfile
echo "color=red" >>  ~/.grc/myfile
grc -c myfile tail -f myfile.log
```

And with [grc] for the second time and all times:

```bash
grc -c myfile tail -f myfile.log
```

- See the `README.markdown` document on [github] for more information.

- For other uses of coloring log files, see [Tail and Multitail](tail.md).

### Method 2: Using aliases

To save time, you can create aliases for your most frequently used commands.
Add the following lines to your ~/.bashrc or ~/.zshrc file:

```bash
alias ping='grc ping'
alias traceroute='grc traceroute'
alias gcc='grc gcc'
alias make='grc make'
```

After adding these aliases, restart your terminal or run source ~/.bashrc or
source ~/.zshrc. Now, when you type ping, it will automatically use grc for
coloring.

## Custom Configuration

`grc` comes with predefined color schemes for some common commands. However,
you can create your own custom color schemes by editing the configuration
files.

### Step 1: Create a custom conf file

Create a new file with the `.conf` extension (for example) in `~/.grc/`, in
general:

```bash
mkdir -p ~/.grc
touch ~/.grc/my_command.conf
```

As an example we create the script `hello-world.sh` with the content:

```bash
#!/bin/bash
echo "Hello world!"
```

```bash
mkdir -p ~/.grc
touch ~/.grc/hello-world.conf
```

### Step 2: Define your custom color scheme

Edit the `~/.grc/my_command.conf` file and define the color scheme using the following format

```conf
regexp=<regular_expression>
colour=<color>
```

In our example, to color the command `hello-world.sh`, create
`~/.grc/hello-world.conf` as done above and add:

```conf
regexp=^(Hello)
colour=green
-
regexp=(world)
colour=red
```

This configuration could already be used like so:

```bash
grc -c 'hello-world.conf' ./hello-world.sh
```

To make it easier to use, an additional step is required.

### Step 3: Add the custom conf file to grc configuration

__Important:__ if you do not have a `~/.grc/grc.conf`, create one by copying
`/etc/grc.conf` to `~/.grc/grc.conf`. Since `grc` uses only one configuration
file, creating a new `~/.grc/grc.conf` will no longer colorize tools configured
in `/etc/grc.conf`.

In general, add 3 lines for each program you colorize to `~/.grc/grc.conf`:

```conf
# COMMAND
REGEX_THAT_DETECTS_THE_COMMAND
GRCAT_CONFIGURATION_FILE_INSIDE_$HOME/.grc/
```

For our example, edit the `~/.grc/grc.conf` file and add at the end: a comment,
a regex to detect `hello-world.sh` and the name of your configuration file
inside `~/.grc/`.

```conf
# hello-world.sh
(^|[/\w\.]+/|./)hello-world.sh\s*
hello-world.conf
```

The format of `/etc/grc.conf` or `~/.grc/grc.conf`: each entry consists of 2
lines, between entries there can be any number of empty lines or lines starting
with "#" (comments).

The first line is the regular expression, the second line is the name of the
configuration file in the format of `grcat`.

Now, you can use `grc` with your custom color scheme.

```bash
grc ./hellow-world.sh
```

To further automate, you would define an alias.

```bash
alias hello-world.sh='grc PATH_TO/hello-world.sh'
```

This would permanently colorize `hello_world.sh`.

## Links

- [grc]
- [github]

[grc]: http://kassiopeia.juls.savba.sk/~garabik/software/grc.html
[github]: https://github.com/garabik/grc

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.3   | 2023-05-12 | Fix Heading                                          |
| 0.1.2   | 2023-04-14 | Improve writing, add examples and steps              |
| 0.1.1   | 2022-05-27 | Update for Debian 11 Bullseye, 9.13 Stretch, change  |
|         |            | shell to bash, +History, Link to tail, +commands     |
| 0.1.0   | 2020-12-17 | Initial release                                      |


