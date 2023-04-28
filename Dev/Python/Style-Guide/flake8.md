---
title: Flake8
author: Christian Külker
date: 2023-04-28
version: 0.1.2
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

Flake8 is a popular Python linting tool that combines the functionality of
several linting tools ([PyFlakes](https://pypi.org/project/pyflakes/),
[pycodestyle](https://pypi.org/project/pycodestyle/)). It helps identify
errors, PEP 8 style guide violations, and code complexity issues, making Python
code more readable, maintainable, and efficient. This document will guide you
through the installation and use of Flake8 with Python on a Debian-based Linux
distribution.

With __flake8__ it is possible to enforce a style guide on hand written Python
code.  By default, __flake8__ includes checks provided by __PyFlakes__,
PEP-0008 inspired style checks provided by __PyCodeStyle__, and __McCabe__
complexity checking.

## Installation

```bash
aptitude install flake8
```

This assumes that you are using the system version of Python.  "It is very
important to install Flake8 on the correct version of Python for your needs. If
you want Flake8 to properly parse new language features in Python 3.5 (for
example), you need it to be installed on 3.5 for Flake8 to understand those
features. In many ways, Flake8 is tied to the version of Python on which it
runs." (Note from <https://flake8.pycqa.org/en/latest/>)

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

## Sources

- [Flake8](https://github.com/PyCQA/flake8)
- [Pyflakes](https://github.com/PyCQA/pyflakes)
- [PyCodeStyle](https://github.com/PyCQA/pycodestyle)

## Usage

There are basically three ways to use and influence flake8:

1. Command line (file or directory)
2. Code Comments
3. Configuration file

The command line tool `flake8` can be used on a single file or on multiple
files, while comments control flake8 for special cases and the configuration
file can control flake8 for one or all files aka the project.

### Single File Usage

```bash
python3 flake8 /PATH/TO/file.py
```

### Multiple File Usage

```bash
python3 flake8 /PATH/TO/FILES/*.py
```

### Output

The `flake8` command prints a list of problems found. While most of them are
style-related, it also prints if it found a variable that was assigned but not
used. (Perl does this automatically via the `-w` option).

## Useful Command Line Options

While __flake8__ can be used without options, it can be customized via a
configuration file or various options. This section shows only a small subset
of useful command line options.

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

To ignore certain violation types, it is possible to specify the ID on the
command line. (It is also possible to specify it in a configuration file)

New exclude list:

```bash
flake8 --ignore=E1,E23,W503 path/to/files/
```

Extending the exclude list:

```bash
flake8 --extend-ignore=E1,E23 path/to/files/
```

### Ignoring Lines

If the line is short enough, it is possible to ignore a line with a special
comment `# noqa`.

```bash
example = lambda: 'example'  # noqa: E731
```

### Flake8 Configuration

Create a `.flake8` file in the project directory.

```bash
touch .flake8
vim .flake8
[flake8]
ignore = E226,E302
max-line-length = 80
exclude = migrations,venv,tests
```

Here we're ignoring [E226](https://www.flake8rules.com/rules/E226.html)
(missing whitespace around arithmetic operators) and
[E302](https://www.flake8rules.com/rules/E302.html) (expected two blank lines,
found 0).  We've also set the maximum line length to 80 characters and excluded
directories named "migrations", "venv" and "tests" from scanning. The call to
`flake8` is as usual.

## Discussion

Having __flake8__ in Python is nice. Compared to other solutions, such as
__perltidy__, it seems that this tool has a long way to go before it is as easy
to use, useful, and time-saving. A short example: When using an editor (like
vim), it is possible to define a hotkey to activate __perltidy__ to format the
displayed code. So as a programmer, you do not have to write all the
indentation and other fancy spacing or alignment. Just press the key and the
code looks nice. With __flake8__, the programmer just gets a report and has to
fix everything manually. __perltidy__ can batch convert files, check files
embedded in other markup (such as HTML), and much more. Since Python, unlike
Perl, has the "there is only one way" syntax philosophy, it is of course no
wonder that __flake8__ does not offer much in the way of syntax customization,
but a real advantage of __flake8__ is that it prints IDs referencing the rules,
which can be used to look up and find more information about specific rules.

## Alternatives

- [Pylint](https://pylint.readthedocs.io/en/latest/)


## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2023-04-28 | Improve writing, add configuration file .flake8      |
| 0.1.1   | 2022-07-06 | History, shell->bash, Debian 11 Bullseye             |
| 0.1.0   | 2020-05-08 | Initial release                                      |

