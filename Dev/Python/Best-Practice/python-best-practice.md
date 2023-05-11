---
title: Python Best Practice
linkTitle: Python-Best-Practice
author: Christian Külker
date: 2023-05-11
version: 0.1.2
type: doc
disclaimer: True
toc: True
categories:
- Best-Practice
tags:
- Python-Best-Practice
description: Python-Best-Practice

---

This guide follows some ideas from `The Hitchhiker's Guide To Python` and other
ideas from the internet and myself. It is a work in progress.

## PEP (Python Enhanced Proposals)

- `PEP 8`: The Python Style Guide. Read this. All of it. Follow it.

- `PEP 257`: `Docstring` Conventions. Gives guidelines for semantics and
conventions associated with Python `docstrings`.

- [PEP Index](http://www.python.org/dev/peps/)

## IDE

This section contains only IDE and editors that I have Python experience with.
Note that this section is incomplete.

### Vim

Make `vim` `PEP8` compatible. Best if `vim` was compiled with `+python`.

~~~
set textwidth=79  " lines longer than 79 columns will be broken
set shiftwidth=4  " operation >> indents 4 columns; << unindents 4 columns
set tabstop=4     " a hard TAB displays as 4 columns
set expandtab     " insert spaces when hitting TABs
set softtabstop=4 " insert/delete 4 spaces when hitting a TAB/BACKSPACE
set shiftround    " round indent to multiple of 'shiftwidth'
set autoindent    " align the new line indent with the previous line
~~~

- If you also use vim for another language, use the [ident] plugin
  (http://www.vim.org/scripts/script.php?script_id=974).

- Improved [syntax](http://www.vim.org/scripts/script.php?script_id=790)
highlighting.

-  `PEP8` compliance[pycodestyle]()

-  `PEP8` compliance [Pyflakes](http://pypi.python.org/pypi/pyflakes/) install
[vim-flake8](https://github.com/nvie/vim-flake8) to check PEP8 from within
`vim`. Map function `Flake8` to a hotkey.

- Check PEP8 (Pyflake) on every save, edit `.vimrc`: `autocmd BufWritePost*.py
call Flake8()`.

- Show PEP8 errors and warnings with
[syntastic](https://github.com/scrooloose/syntastic) in the quickfix window.

```vim
set statusline+=%#warningmsg#
set statusline+=%{SyntasticStatuslineFlag()}
set statusline+=%*
let g:syntastic_auto_loc_list=1
let g:syntastic_loc_list_height=5
```

- Use the [Python mode](https://github.com/klen/python-mode): Python code
  checking with pylint, pyflakes, pycodestyle, or mccabe; refactoring,
  autocompletion with [Rope](https://github.com/python-rope/rope); virtualenv
  support, documentation search.

- Use [SuperTab](http://www.vim.org/scripts/script.php?script_id=1643) for code
completion with the tab key.

## Tools

### Pip

Install `pip` if not already done.

### IPython

[IPhthon](http://ipython.org/) should be used interactively with the features:
shell, web-based notebook, data visualization, GUI toolkits, parallel
execution.

```bash
pip install ipython      # base install only
pip install ipython[all] # notebook qtconsole, tests, ...
```

## Environment

Use __virtual environments__ on a per project base (also with `pip`).

Bash: (for `pip install`, this will make `pip` require a virtual environment)

```bash
export PIP_REQUIRE_VIRTUALENV=true
```

See this example environment.

```bash
#!/usr/bin/zsh
VER=3.10.2
# Disable the creation if bytecode (should be enabled)
# export PYTHONDONTWRITEBYTECODE=1

# pip only work with virtual enviroments (prevents accidental global installs)
# Or edit pip.conf, pip.ini
export PIP_REQUIRE_VIRTUALENV=true

# cache often used libraries, or edit pip.conf, pip.ini
if [ ! -d $HOME/.pip/cache ]; then mkdir -p $HOME/.pip/cache; fi
export PIP_DOWNLOAD_CACHE=$HOME/.pip/cache

# To install globally use 'gpip'
gpip() {
    PIP_REQUIRE_VIRTUALENV="" pip "$@"
}

LOC=/usr/local/bin
BIN=$LOC/python3.10
DIR=/srv/build/Python-$VER
if [  -f $BIN ];then
    #echo "BIN [$BIN] exists"
    export PATH=$LOC:$PATH
    alias -g python3="$BIN"
fi
```

## Project Layout

This is the layout of the project `sample`

~~~
README.rst
LICENSE             # full license text
Makfile             # not mandatory
setup.py            # package distribution management
requirements.txt    # pip requirements file
sample/__init__.py  # 'sample' is the project name, mostly empty
sample/core.py      # 'sample' is the project name
sample/helpers.py   # 'sample' is the project name
docs/conf.py        # package reference documentation
docs/index.rst      # package reference documentation
tests/test_basic.py
tests/test_advanced.py
~~~

- Use the directory `sample` (and replace this with a real name) and do not use
  `src` or `python`.

## Makefile

```makefile
init:
	pip install -r requirements.txt
test:
	py.test tests
.PHONY:init test
clean:
	# remove bytecode
	find . -type f -name "*.py[co]" -delete -or -type d -name "__pycache__" -delete
```

## Testing

- Use a simple and explicit path modification to resolve the package properly

- Individual tests should have an import context, create a `tests/context.py` file:

```python
import os
import sys
sys.path.insert(0,os.path.abspath(os.path.join(os.path.dirname(__file__),'..')))

import sample
```

Alternatively this can be done in `tests/__init__.py`.

- Within the individual test modules: import the module like so

```python
from.context import sample
```

## Modules

- Module names should be short
- Module names should not contain spaces
- Module names should not contain hyphens

### Import

- Do not use `from module import *`
- Might do `from module import function` might be OK

```python
"""Bad:"""
        from MODULE import *
"""
        FUNCTION(PARAMETER)
"""

"""Better:"""
        from MODULE import FUNCTION
"""
        FUNCTION(PARAMETER)
"""

"""Best:"""
        import MODULE
"""
        MODULE.FUNCTION(PARAMETER)
"""
```

- For nested package use: `import dir1.dir2.module as mod`.


## Packages

- A directory with an `__init__.py` file is a `package`
- Do not add too many code to `__init__.py`
- It is considered a good practice to leave `__init__.py` emtpy

The command `import pack.module` will look for the file `pack/__init.py__` and
execute all top level statements and then look for `pack/module.py` and execute
all top level statements.

## Function

Use stateless functions, if possible, because **pure functions** are:

- __deterministic:__ the output will always be the same (for same input)
- __easier to change or replace:__ if needed
- __easier to test:__ with unit tests (less need complex context setup and data
  cleaning afterwards)
- __easier to manipulate, decorate, and pass around__

## Decorators

- Use decorator syntax

## Context Managers

- Use content managers

## Dynamic Typing

- Do not use same variable for different types of content

## Mutable (Changeable) and Immutable (Fixed) Types

- Mutable Types should not be used for dictionary keys

```python
"""Bad"""
nums = ""
for n in range(20):
    nums += str(n) # slow and inefficient
print nums

"""Good"""
nums = []
for n in range(20):
nums.append(str(n))
print "".join(nums) # much more efficient

"""Better"""
nums = [str(n) for n in range(20)]
print "".join(nums)

"""Best"""
nums = map(str, range(20))
print "".join(nums)
```

```python
foo = 'foo'
bar = 'bar'
foobar = foo + bar # This is good
foo += 'ooo' # This is bad, instead you should do:
foo = ''.join([foo, 'ooo'])
```

```python
foo = 'foo'
bar = 'bar'
foobar = '%s%s' % (foo, bar) # It is OK
foobar = '{0}{1}'.format(foo, bar) # It is better
foobar = '{foo}{bar}'.format(foo=foo, bar=bar) # It is best
```

## Explicit Code

```phyton
"""Bad"""
def make_complex( *args):
    x, y = args
    return dict( **locals())
"""Good"""
def make_complex(x, y):
    return {'x': x, 'y': y}
```

## Statement Per Line

- Use one statement per line

## Function Parameters (Arguments)

- Easy to read (the name and arguments need no explanations)

- Easy to change (adding a new keyword argument does not break other parts of
  the code)

### Positional Arguments

- Should be used for simple functions

```python
   print_at(x,y)
   draw_line(x1,y1,x2,y2)
```

### Keyword Arguments

- Often used with optional parameters

```python
    send(message, to, cc=None, bcc=None)
```

### Arbitrary Argument List

- Use carefully or avoid it. This can often not be revoked when removing
parameters, in a later stage of the application

```python
"""Bad"""
send(message, *args)
send('Hello', ['Bilbo', 'Frodo', 'Sauron']
"""Better"""
send(message, recipients)
send('Hello', ['Bilbo', 'Frodo', 'Sauron']
```

### Arbitrary Keyword Argument Dictionary

- Use carefully only if needed.
- **kwargs

## Magic

- Do not use magic
- Do not change how objects are created and instantiated
- Do not change how the Python interpreter imports modules
- Try not to embed C routines in Python, if possible

## Be a Nice User

- Do not write client code that can override an object’s properties and methods

## Return Values

- Try to avoid multiple places for return statements

## Documentation

Although there is usually one way to do it. It seems that when it comes to
documentation, there are more.

Python is no exception, suggesting that a `README` file be maintained for
project documentation rather than an `INSTALL` document, since installation
methods are usually known and similar. The Python manual suggests the presence
of a `LICENSE` file.

If the project gets bigger and the `README` gets too long (only then), parts of
the `README` might be moved to a `TODO` file and a `CHANGELOG` file.

For the format `reStructuredText` or `Markdown` is suggested.

This is usually sufficient for small and medium-sized projects. Often, the
death knell of an emerging open source software project comes when the project
reaches a size where a README is no longer sufficient. Then a wiki, or even a
homepage, is suggested, and political factions form, sometimes engaging in
fierce bike-shed color wars. The following short section is for those
situations.

Shpinx is a popular Python documentation tool that uses reStructuredText and
spits out HTML and PDF.

For other parts, it is advisable to document the source code already.  Python
can be well documented using `docstrings` (`PEP 0257#specification`) in favor
of block comments (`PEP 8#comments`).

A `docstring` comment is basically a multi-line comment with three quotes
around it, and in some cases they can be used to supplement unit tests.

```python
def add_two_numbers(x, y):
    """
    SUM = add_two_numbers(NUMBER_0, NUMBER_1)

    - Combine two NUMBERS via the operation of addition
    - Require a two NUMBERS: NUMBER_0, NUMBER_1
    - Return the sum of 2 NUMBERS
    """
    return x + y
```

Of course in such an obvious case a one line `docstring` comment is apropriate

```python
def add_two_numbers(x, y):
    """Add two numbers and return the sum."""
    return x + y
```


## Bytecode

- Do not commit `*.pyc` files to the source code repository
- Can be disabled with `export PYTHONDONTWRITEBYTECODE=1` (but probably should
  not)

## Git

### .gitignore

```.gitignore
*.pyc
*.pyo
*.pyd
__pycache__
```

Or

```.gitignore
  # Will match .pyc, .pyo and .pyd files.
*.py[cod]
  # Exclude the whole folder
__pycache__/
```

## License

- In the US a license file is needed
- Permissive licenses:
  - PSFL(Python Software Foundation License): contributing to Pyhton
  - MIT (X11)/ new BSD/ ISC/ Apache
- Less permissive licenses:
  - LGPL
  - GPL (GPLv2, GPLv3)

## Package

- [guide](https://python-packaging-user-guide.readthedocs.io/)
- Use pip (and not easy_install)

## Documentation

### The Hitchhiker's Guide To Python

- [Online](https://docs.python-guide.org/)
- [Book](https://www.thalia.de/shop/home/suggestartikel/ID45071037.html)
- [PDF](https://buildmedia.readthedocs.org/media/pdf/python-guide/latest/python-guide.pdf)
- [github](https://github.com/realpython/python-guide)

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2023-05-11 | Improve writing, add environemnt, add link.          |
| 0.1.1   | 2022-06-09 | Shell->bash, +history, documtation                   |
| 0.1.0   | 2020-01-18 | Initial release                                      |


