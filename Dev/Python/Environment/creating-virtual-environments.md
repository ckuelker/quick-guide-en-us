---
title: Creating Virtual Environments
author: Christian Külker
date: 2022-07-12
version: 0.1.1
type: doc
disclaimer: True
toc: True
categories:
- Python
- Environment
commands:
- virtualenv
- venv
- find
- python3
- source
tags:
- virtual environments
- bash
- zsh
- tcsh
- csh
- pipenv
- pyenv
- plenv
- plerlbrew
- local::lib
- carton
- pip
- cpanminus
description: Creating virtual Python environments with venv and virtualenv

---

The tool [virtualenv] uses the command-line path environment variable to
create isolated **Python Virtual Environments**, much as [venv] does.
Compared to **venv**, **virtualenv** provides additional functionality by
supporting Python 2.7 and by providing convenient features for configuring,
maintaining, duplicating, and troubleshooting the virtual environments.

Python **Virtual Environments** allow Python packages to be installed in an
isolated (local) location, rather than being installed globally.

Currently, there are two common tools for creating Python virtual environments:

* [venv] (Python 3.4+) is part of Python since Python 3.3
* [virtualenv] (Python 2.7+ and Python 3.3+)

See [Pipenv] on how to manage multiple virtual environments. The recommended
tool **pyvenv** for creating virtual environments for Python 3.3 and 3.4 is
deprecated in Python 3.6. The use of [venv] is from Python 3.5 recommended for
creating virtual environments.

## Basic Usage venv

For **bash** or **zsh**:

```bash
python3 -m venv <DIR>
source <DIR>/bin/activate
```

For **csh** or **tcsh**:

```bash
python3 -m venv <DIR>
source <DIR>/bin/activate.csh
```

## Basic Usage virtualenv

```bash
virtualenv <DIR>
source <DIR>/bin/activate
```

### Example

Without any other information it sets up a python2 environment

```bash
virtualenv /tmp/test-virtualenv
Running virtualenv with interpreter /usr/bin/python2
New python executable in /tmp/test-virtualenv/bin/python2
Also creating executable in /tmp/test-virtualenv/bin/python
Installing setuptools, pkg_resources, pip, wheel...done.
find test-virtualenv |wc -l
1141
```

## Basic Creation venv

```bash
python3 -m venv /path/to/new/virtual/environment
```

A common name for the target directory is `.venv`.

### Example

This creates a horrible long list of files.

```bash
python3 -m venv /tmp/test-venv
cd /tmp/test-venv
find|wc -l
448
```

## Excursus On Perl Virtual Environments

Virtual environments are well known in Perl. And there are basically two
reasons why you would manage and use Perl virtual environments: (1) Some
conflicting Perl modules. (2) A controlled environment. While I came across (2)
more than once I actually never experienced (1) in the last 25 years. While I
think it is possible, it seems seldom to be an issue as modules tend to upgrade
in Perl quite smoothly. As my experience with Python is less long, I am
wondering why virtual environments are suggested to be used so many times.

| Scope                       | Python           | Perl                |
| --------------------------- | ---------------- | ------------------- |
| Manage Language Versions    | pyenv            | plenv, plerlbrew    |
| Manage Module Versions      | venv, virtualenv | local::lib, carton  |
| Manage Module Installations | pip              | cpanminus           |

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2022-07-12 | History, shell->bash                                 |
| 0.1.0   | 2020-05-16 | Initial release                                      |

[venv]: https://docs.python.org/3/library/venv.html
[virtualenv]: http://virtualenv.pypa.io/
[Pipenv]: https://packaging.python.org/key_projects/#pipenv

