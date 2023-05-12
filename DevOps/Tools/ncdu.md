---
title: Ncdu
author: Christian Külker
date: 2023-05-12
version: 0.1.1
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Tools
commands:
- ncdu
tags:
- Ncdu
description: NCurses Disk Usage

---

## NCDU (NCurses Disk Usage)

NCDU (NCurses Disk Usage) is a powerful command line utility that allows you to
analyze disk usage in an easy-to-use and interactive way. It is especially
useful when you need to find large files or directories that are taking up a
lot of disk space.

### Installation

To install NCDU on Debian, use the following command:

```bash
aptitude install ncdu
```

### Basic Usage

To start the analyzing disk usage in the current directory, simply run:

```bash
ncdu
```

NCDU will scan the directory and display an interactive and navigable list of
files and directories sorted by size.

### Command Options

You can customize the behavior of NCDU in several ways:

#### `-q` - Quiet mode

```bash
ncdu -q
```

While scanning or importing the directory, ncdu refreshes the screen 10 times
per second by default, this is reduced to once every two seconds in quiet mode.
Use this feature to save bandwidth over remote connections. This option has no
effect if "-0" is used.


####  `-0` - No feedback

Don't give any feedback while scanning a directory or importing a file, unless
a fatal error occurs. Ncurses is not initialized until the scan is complete. If
you export the data with "-o", ncurses is not initialized at all. This option
is the default when exporting to standard output.

#### `-1` - Some Feedback

Similar to "-0", but provides feedback on the progress of the scan with a
single line of output. This option is the default when exporting to a file.

#### `-x` - Stay on filesystem

Only analyze files and directories that are on the same file system as the
initial directory:

```bash
ncdu -x
```

#### `-X` - Exclude files

Exclude files that match a pattern specified in a file. Patterns should be
separated by a newline.

```bash
ncdu -X exclude.txt
```

#### `--exclude` - Exclude pattern

Exclude files that match a specified pattern:

```bash
ncdu --exclude '*.mp4'
```

These files are listed together at the end and are 0 bytes in size.

### Exporting Results

Use the `-o` option to export the results of a scan to a file:

```bash
ncdu -o output.out
```

You might consider using '-1' to remove unwanted output, or even -0 to remove
even more messages in the case of cron.

### Links

- Source: <https://g.blicky.net/ncdu.git/>
- Home: <https://dev.yorhel.nl/ncdu>
- Third party tool [not tested]: <https://github.com/wodny/ncdu-export>
- Third party tool [not tested]: <https://github.com/lilydjwg/ncdu-diff>
  Howerver that reccomends: gdu + gdu-diff

### Conclusion

NCDU is a powerful and easy-to-use tool for analyzing disk usage on Linux
systems. With its interactive interface and various options, it can help you
quickly identify large files and directories, making command line disk space
management faster.

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2023-05-12 | Fix frontmatter YAML                                 |
| 0.1.0   | 2023-04-15 | Initial release                                      |
 
