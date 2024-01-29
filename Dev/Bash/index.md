---
title: Bash
author: Christian Külker
date: 2024-01-29
version: 0.1.2
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Bash
commands:
- printf
tags:
- Index
description: Index

---

## Introduction

Writing a line with equal signs or hyphens in Perl or Python is straight
forward. However doing this in bash bash required a different `printf`
format string.

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2024-01-29 | Fix code fences                                      |
| 0.1.1   | 2024-01-25 | Add python, history, introduction                    |
| 0.1.0   | 2023-05-15 | Initial release                                      |

## String Composed of 80 Equals

Perl

```perl
my $line = "=" x 80;
```

Python

```python
line = "=" * 80
```

Bash

```bash
line=$(printf '=%.0s' {1..80})
```

## String Composed of 80 Hyphens

Unlike 'String Composed of 80 Equals' the solution has to be different,
otherwise `printf` gets confused.

Perl

```perl
my $line = "-" x 80;
```

Python

```python
line = "-" * 80
```

Bash

```bash
line=$(printf '%0.s-' {1..80})
```
