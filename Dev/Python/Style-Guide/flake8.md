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
description: Enforcing a style guide with flake8

---

With **flake8** it is possible enforcing a style guide to hand written python
code.  By default **flake8** includes checks provided by **PyFlakes**, PEP-0008
inspired style checks provided by **PyCodeStyle**, and **McCabe** complexity
checking.

## Installation

```shell
aptitude install flake8
```

## Usage

The command line tool `flake8` can be used on a single file or on multiple
files.

### Single File Usage

```shell
python3 flake8 /PATH/TO/file.py
```

### Multiple File Usage

```shell
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

Using **flake8**, I did not found a method to disable the report of one
specific occurrence of a syntax violations on a specific line, like it is
possible to silence **perltidy** for one specific code line. There are cases
when longer function names or variable names make code clearer. It seems quite
strange, just to silence **flake8** to rename those, disable the whole issue ID
or to spend more time on formatting, than to write the function in the first
place.

