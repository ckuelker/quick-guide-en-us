---
title: Ripgrep
linkTitle: Ripgrep
author: Christian Külker
date: 2023-03-30
version: 0.1.0
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Tools
commands:
- rg
tags:
- Ripgrep
description: Ripgrep the faster grep with file logic build in and unicode support

---

There are differences between ripgrep and standard grep:

1. Speed: ripgrep is designed to be faster than grep and other similar tools.
   It does this by using multiple threads to search for patterns in files,
   taking advantage of modern CPUs.

2. File selection: ripgrep has built-in file selection logic that allows it to
   search for patterns in specific types of files (e.g., text files, source
   code files, etc.) without the need for complex command-line options. It
   excludes files mentioned in `.gitignore`, `.ignore`, `.rignore`
   automatically.

3. Unicode support: ripgrep supports Unicode by default, which means it can
   search for patterns in text that contains non-ASCII characters that grep may
   not be able to handle properly.

Overall, `ripgrep` is a modern, powerful tool designed to be a faster and more
user-friendly alternative to `grep`. It can be especially useful for searching
large codebases and other collections of files.

You should use `grep` instead if POSIX compatibility is important.

## Installation

```bash
aptitude install ripgrep
```

## Usage

In this example, the `rg` command will search for the string "search term" in
all files under the directory specified by `/path/to/directory`. By default,
`rg` ignores hidden files and directories (i.e., those that begin with a dot).

```bash
rg "search term" /path/to/directory
```

This command searches for the string "search term" in all files with a `.py`
extension (i.e., Python files). The `--type` option tells rg to search only in
files of the specified type. You can replace python with any other supported
file type (e.g. ruby, markdown, json, etc.).

```bash
rg "search term" --type=python
```

## Source

- <https://github.com/BurntSushi/ripgrep>

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2023-03-30 | Improve writing and initial release                  |







