---
title: Managing Dependencies With Pipenv
author: Christian Külker
date: 2023-03-06 
version: 0.1.4
type: doc
disclaimer: True
toc: True
PDF: False
categories:
- Python
- Python3
- Environment
commands:
- pipenv
- python3
tags:
- Pipfile
- Pipfile.lock
- sha256
- setuptools
- wheel
- pip
- Managing-Dependencies-Virtually
description: Managing Python dependencies virtually with pipenv

---

## Introduction

Python is used for many different purposes, and how you want to manage your
dependencies may change depending on how you decide to publish the software.
This document shows some tools for different types of software: applications
and libraries.

## Managing Application Dependencies

With some [caveats], `pipenv`, mentioned in the [Python Packaging Guide] (from
which this section got its [inspirations]), is a tool for managing the
dependencies of Python applications (as opposed to Python libraries).

**Pipenv** is a dependency manager for Python projects, similar to `npm`
(Node.js), which may be handy for development, but comes with all the drawbacks
of a shared library environment: reinstalling similar things per user per
application, wasting time and disk space.

### Installing Pipenv

```bash
python3 -m pip install --user pipenv

Collecting pipenv
  Downloading https://files.pythonhosted.org/packages/13/b4/\
3ffa55f77161cff9a5220f162670f7c5eb00df52e00939e203f601b0f579/\
pipenv-2018.11.26-py3-none-any.whl (5.2MB)
    100% |████████████████████████████████| 5.2MB 155kB/s
Requirement already satisfied: virtualenv in /usr/lib/python3/dist-packages \
(from pipenv) (15.1.0)
Requirement already satisfied: pip>=9.0.1 in /usr/lib/python3/dist-packages \
(from pipenv) (18.1)
Requirement already satisfied: setuptools>=36.2.1 in /usr/lib/python3/\
dist-packages (from pipenv) (40.8.0)
Requirement already satisfied: certifi in /usr/lib/python3/dist-packages (\
from pipenv) (2018.8.24)
Collecting virtualenv-clone>=0.2.5 (from pipenv)
  Downloading https://files.pythonhosted.org/packages/83/b8/\
cd931487d250565392c39409117436d910232c8a3ac09ea2fb62a6c47bff/\
virtualenv_clone-0.5.4-py2.py3-none-any.whl
Installing collected packages: virtualenv-clone, pipenv
  The script virtualenv-clone is installed in '/home/$USER/.local/bin' which \
is not on PATH.
  Consider adding this directory to PATH or, if you prefer to suppress this \
warning, use --no-warn-script-location.
  The scripts pipenv and pipenv-resolver are installed in '/home/$USER/.local\
/bin' which is not on PATH.
  Consider adding this directory to PATH or, if you prefer to suppress this \
warning, use --no-warn-script-location.
Successfully installed pipenv-2018.11.26 virtualenv-clone-0.5.4
```

This will "successfully" install `pipenv`. What is interesting is what kind of
definition of "successful" is used in this context.

```bash
pipenv
zsh: command not found: pipenv
```
However as the "consider" suggests:

```bash
export PATH=/home/$USER/.local/bin:$PATH
cd python-packaging-tutorial-example-package
pipenv install requests

Creating a virtualenv for this project…
Pipfile: /home/$USER/g/github.com-ckuelker/\
python-packaging-tutorial-example-package/Pipfile
Using /usr/bin/python3 (3.7.3) to create virtualenv…
⠼ Creating virtual environment...Already using interpreter /usr/bin/python3
Using base prefix '/usr'
New python executable in /home/$USER/.local/share/virtualenvs/\
python-packaging-tutorial-example-package-UF30b1bC/bin/python3
Also creating executable in /home/$USER/.local/share/virtualenvs/\
python-packaging-tutorial-example-package-UF30b1bC/bin/python
Installing setuptools, pkg_resources, pip, wheel...done.

✔ Successfully created virtual environment!
Virtualenv location: /home/$USER/.local/share/virtualenvs/\
python-packaging-tutorial-example-package-UF30b1bC
Creating a Pipfile for this project…
Installing requests…
Adding requests to Pipfile's [packages]…
✔ Installation Succeeded
Pipfile.lock not found, creating…
Locking [dev-packages] dependencies…
Locking [packages] dependencies…
✔ Success!
Updated Pipfile.lock (444a6d)!
Installing dependencies from Pipfile.lock (444a6d)…
  🐍   ▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉ 5/5 — 00:00:02
To activate this project's virtualenv, run pipenv shell.
Alternatively, run a command inside the virtualenv with pipenv run.
```

This creates 2 files: `Pipfile`

```ini
[[source]]
name = "pypi"
url = "https://pypi.org/simple"
verify_ssl = true

[dev-packages]

[packages]
requests = "*"

[requires]
python_version = "3.7"
```

And `Pipfile.lock` (`sha256` hashes are truncated in this output!)

```json

    "_meta": {
        "hash": {
            "sha256": "bb57e0d7853b45999e472fde31c527d8d7b5b5539dc979444a6d"
        },
        "pipfile-spec": 6,
        "requires": {
            "python_version": "3.7"
        },
        "sources": [
            {
                "name": "pypi",
                "url": "https://pypi.org/simple",
                "verify_ssl": true
            }
        ]
    },
    "default": {
        "certifi": {
            "hashes": [
                "sha256:1d987a998c75633c40804906c920a7f17ef374f5aa4282abd304",
                "sha256:51fcb31174be6e6664c2d72a1a12e90f872cbdb1567eb47b6519"
            ],
            "version": "==2020.4.5.1"
        },
        "chardet": {
            "hashes": [
                "sha256:84ab92ed1c4d4f169166c0fb5db821cc65e70cbd64a3e2a5eaae",
                "sha256:fc323ffcaeaed0e0a028aed530d9ed4531e3e15460124c106691"
            ],
            "version": "==3.0.4"
        },
        "idna": {
            "hashes": [
                "sha256:7588d1c14ae4c77d740b26d0fde8f007354fd48a7814db15b7cb",
                "sha256:a068a21ceac8a4d63db07f541babbd2250d61922f029858365fa"
            ],
            "version": "==2.9"
        },
        "requests": {
            "hashes": [
                "sha256:43999036bfa82904b6a60e5e2c68e5c4b0aa03b655f3d7d73fee",
                "sha256:b3f43d496c6daba44937dbc6288f52a6e04e7b6023b0247817e6"
            ],
            "index": "pypi",
            "version": "==2.23.0"
        },
        "urllib3": {
            "hashes": [
                "sha256:3018294ebefce6572a4e33b3fd8006ecd11d62107a5d2a963527",
                "sha256:88206b0eb87e6d677d43fb9d0190d0ee169599165ec25e9d9115"
            ],
            "version": "==1.25.9"
        }
    },
    "develop": {}
}
```

Where the dependencies on `certifi`, `chardet`, `idna`, `requests` and
`urllib3` come from is not entirely clear. I could be wrong, but it seems like
a waste of resources.

To better test this, your sample project should have some dependencies. The
project above has only `setuptools`, `pkg_resources`, `pip` and `wheel`. To
test this, create a `main.py` file with your dependencies: like `mydep`. Here
is some pseudo code.

```python
import mydep

result = mydep.action()

print('the result of mydep actyion: ' + result)
```

Run the code like.

```bash
pipenv run python main.py
```

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.4   | 2023-03-06 | Improve writing                                      |
| 0.1.3   | 2022-07-13 | Shell->bash, move history, formatting                |
| 0.1.2   | 2022-05-10 | Fix json syntax highlighting                         |
| 0.1.1   | 2022-05-09 | PDF: False (no ⠼ (U+283C), 🐍 (U+1F40D) in font      |
|         |            | WenQuanYi Micro Hei Mon//OT:script=lat)              |
| 0.1.0   | 2020-05-18 | Initial release                                      |

[Python Packaging Guide]: https://packaging.python.org/
[inspirations]: https://packaging.python.org/tutorials/managing-dependencies/
[caveats]: https://packaging.python.org/tutorials/managing-dependencies/#recommendation-caveats-as-of-april-2020

