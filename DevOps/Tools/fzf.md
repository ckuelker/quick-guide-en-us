---
title: Fzf
author: Christian Külker
date: 2023-02-16
version: 0.1.0
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Tools
commands:
- fzf
tags:
- Fzf
description: Search or match a list in a fuzzy way

---

`fzf` is an interactive filter program that can be used to search for items in
a list. For example, it can be used to interactively match filenames in a list
of files.

## Installation

    aptitude install fzf

## Source

Source: https://github.com/junegunn/fzf

## Usage

### Safe Results in a File

Search for 'book' in PDF files interactively and save the result in
`results.txt`:

```bash
# Run
find / -name "*.pdf*"|fzf > results.txt
# Then type 'book'
# The results are displayed in a list with one line highlighted.
# Press <ENTER> to save that line into 'results.txt' and end the fzf session
```

### Edit the Result in One Go

```bash
# This will search all files below the current directory
# The selected file will be opened in vim
fzf --print0 | xargs -0 -o vim
# The same, shorter, but less robust would be
vim $(fzf)
```

### Chaining patterns

You can use the interactive technique above to search for complex things.

1. Find a file that contains exactly the word 'book'
2. And is a Markdown file
3. And the path starts with 'scratch'

You would enter interactively

    'book .md$ ^scratch

This could for example give file `scratch/todo/books-to-write.md`.

| fzf      | Kind of matching            | Notes                              |
| -------- | --------------------------- | ---------------------------------- |
| smthng   | fuzzy-match                 | Items that match smthng            |
| 'calm    | exact-match (quoted)        | Items that include calm            |
| ^movie   | prefix-exact-match          | Items that start with movie        |
| .pdf$    | suffix-exact-match          | Items that end with .pdf           |
| !water   | inverse-exact-match         | Items that do not include water    |
| !^movie  | inverse-prefix-exact-match  | Items that do not start with movie |
| !.pdf$   | inverse-suffix-exact-match  | Items that do not end with .pdf    |

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2023-02-16 | Initial release                                      |


