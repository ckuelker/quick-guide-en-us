---
title: Flake8
author: Christian Külker
date: 2020-05-08
type: doc
disclaimer: True
toc: True
categories:
- Python
- Style-Guide
tags:
- Flake8
- PyFlakes
- PEP-0008
- PyCodeStyle
- McCabe
- Perltidy
commands:
- aptitude
- flake8
description: Enforcing a style guide with flake8

---

With **flake8** it is possible enforcing a style guide to hand written python
code.  By default **flake8** includes checks provided by **PyFlakes**, PEP-0008
inspired style checks provided by **PyCodeStyle**, and **McCabe** complexity
checking.

## Installation

```bash
aptitude install flake8
```

## Versions

Debian Stretch

```bash
flake8 --version
3.2.1 (mccabe: 0.5.3, pyflakes: 1.3.0, pycodestyle: 2.2.0) CPython 3.5.3 on Linux
```

Debian Buster

```bash
flake8 --version
3.6.0 (mccabe: 0.6.1, pycodestyle: 2.4.0, pyflakes: 2.0.0) CPython 3.7.3 on Linux
```

Debian Bullseye

```bash
flake8 --version
3.8.4 (mccabe: 0.6.1, pycodestyle: 2.6.0, pyflakes: 2.2.0) CPython 3.9.2 on Linux
```

## Usage

The command line tool `flake8` can be used on a single file or on multiple
files.

### Single File Usage

```bash
python3 flake8 /PATH/TO/file.py
```

### Multiple File Usage

```bash
python3 flake8 /PATH/TO/FILES/*.py
```

### Output

The command `flake8` prints a list of issues found. While most of them are
related to style it also prints if it found a variable assigned but not used.
(Perl is doing this automatically via `-w` option).

## Useful Command Line Options

While **flake8** is usable already without options, it can be customized via
a configuration file or various options. This sections shows only a small
subset of useful command line options.

* `-q --quite`: Just print the name of file in which issues are found
* `--config=CONFIG`: specify the configuration file (for custom options)
* `--max-line-length=n`: set different line length (default 79)

## Ignoring Errors (Violations)

### Ignoring Files Or Directory

To ignore whole files or directories

```bash
flake8 --exclude path/to/file
```

### Ignoring Error (Violation) Types

To ignore certain error (violation) types, it is possible to provide the ID on
the command line. (It is also possible to it in a configuration file)

New exclude list:

```bash
flake8 --ignore=E1,E23,W503 path/to/files/
```

Extending the exclude list:

```bash
flake8 --extend-ignore=E1,E23 path/to/files/
```

### Ignoring Lines

If the line is short enough it is possible to ignore one line with a special
comment `# noqa`.

```bash
example = lambda: 'example'  # noqa: E731
```

## Discussion

Having **flake8**  under Python is nice to have. Compared with other solutions,
like **perltidy**, it seems this tool needs to go a long way before it is
as easy to use, useful and time saving. A short example: When using an editor
(like vim) it is possible to define a hot key to activate **perltidy** to
format the displayed code. So as a programmer one do not need to write all the
indentations and other fancy spaces or alignments. Just hit that key and the
code looks beautiful. With **flake8** the programmer gets just a report and has
to fix all manually. **perltidy** can batch convert files, check files embedded
in other markup (HTML for example) and much more. As Python has in contrast to
Perl the syntax philosophy "there is only one way" it is of course no
wonder that **flake8** do not offer much to customize syntax, but one real
advantage of **flake8** is, that it print out IDs referencing the rules, that
can be used up to look up and find more information about specific rules.

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2022-07-06 | History, shell->bash, Debian 11 Bullseye             |
| 0.1.0   | 2020-05-08 | Initial release                                      |

