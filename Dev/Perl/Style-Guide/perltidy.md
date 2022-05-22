---
title: Perltidy
author: Christian Külker
date: 2022-05-22
type: doc
disclaimer: True
TOC: True
categories:
- Perl
- Style-Guide
commands:
- perltidy
- vim
tags:
- Perltidy
- PBP
- Perls Best Practice
- vimrc
description: Perl style guide code checking and formatting

---

Perltidy is a very valuable tool when it comes to formatting Perl source code
in a common way. This is especially helpful if you follow the recommended Perl
Best Practices (PBP) style guide.

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.4   | 2022-05-22 | Changes->History, change comments, improve ,pt       |
| 0.1.3   | 2020-05-12 | Add front matter                                     |
| 0.1.2   | 2016-06-22 | Improve vim section                                  |
| 0.1.1   | 2009-03-06 | Adapt changes from PBP                               |
| 0.1.0   | 2009-02-05 | Initial release                                      |


## Installation

```bash
aptitude install perltidy
```

## Configuration

__Perltidy__ uses its configuration file at: `~/.perltidyrc`

## Example Configuration Inspired by Perl Best Practice

```
### PBP perltidy version 0.1 (2009-03-06)

### Max line width is 78 cols
-l=78

### Indent level is 4 cols
-i=4

### Continuation indent is 4 cols
-ci=4

### Output to STDOUT
-st

### ckuelker 2009-02-05: if you switch this on in vim it will print error
### messages directly in your code! Happy clean up!!
### Errors to STDERR
  #-se

### Maximal vertical tightness
### switch this on (set this to 2) seems to be make the layout too tight
### switch this on might be a mistake in PBP (German edition) since PBP
### recommends K&R style, see chapter 2
### ON:
###            my $dog = CipUX::Dog->new(
###                {   dog_hr       => $dog_hr,
###                    object       => $object,
###                    overwrite_hr => $overwrite_hr,
###                }
###            );
### OFF:
###            my $dog = CipUX::Dog->new(
###                {
###                    dog_hr       => $dog_hr,
###                    object       => $object,
###                    overwrite_hr => $overwrite_hr,
###                }
###            );
  #-vt=2

### No extra indentation for closing brackets
-cti=0

### Medium parenthesis tightness
-pt=1

### Medium brace tightness
-bt=1

### Medium square bracket tightness
-sbt=1

### Medium block brace tightness
-bbt=1

### No space before semicolons
-nsfs

### Don't outdent long quoted strings
-nolq

### Break before all operators
-wbb="% + - * / x != == >= <= =~ !~ < > | & >= < = **= += *= &= <<= &&= -= /= \
|= >>= ||= .= %= ^= x="
```

* Download: [./perltidyrc](perltidyrc)

## Activation In Vim

To activate ``perltidy`` cleanup in vim you can add this to `~/.vimrc`

```vim
map ,pt <Esc>:%!perltidy <CR>
```

When pressing ',pt' fast after each other your Perl code gets reformatted if
``perltidy`` is installed in your path. Otherwise you code gets removed.

In case unintentional removing of content is not desired the above mapping
can be made safer by adding a vim function.

```vim
function! s:Perltidy() abort
  if executable("perltidy")
    :%!perltidy
    echo "Executed perltidy"
  else
    echo "- FAIL: Could not find command 'perltidy'"
  endif
endfunction
map ,pt :call <sid>Perltidy()<CR>
```
