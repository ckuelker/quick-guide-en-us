---
title: Syntax Highlighting
linkTitle: Highlighting
author: Christian Külker
version: 0.1.0
date: 2025-04-04
type: doc
locale: en_US
lang: en
disclaimer: True
toc: True
categories:
- Vim
commands:
- vim
tags:
- syntax-highlighting
discription: This document explains how to change syntax highlighting in vim using vim-pandoc and vim-pandoc-syntax

---

## Introduction

This document describes detail of syntax highlighting. The examples are
verified with `vim-pandoc/vim-pandoc` together with
`vim-pandoc/vim-pandoc-syntax`

## History

| Version | Date       | Notes                                               |
| ------- | ---------- | --------------------------------------------------- |
| 0.1.0   | 2025-04-04 | Initial release                                     |

## Exploration

To understand what file type the current buffer has use `:echo &filetype`. This
might print `markdown` or `pandoc` for example. Then to understand the current
mapping use `:syntax list`. This is a very long list. It is possible to store
the list like so:

__Printing Syntax List to a File__

```vim
:redir! > ~/vim-syntax.txt
:silent syntax list
:redir END
```

__Understanding What Highlight Groups Exists__

To understand the highlight group under the cursor use this statement

```vim
:echo synIDattr(synID(line('.'), col('.'), 1), 'name')
pandoxAtxStart
:echo synIDattr(synID(line('.'), col('.'), 1), 'name')
pandoxAtxHeader
```

### Header Manipulation

__Changing the concealed character for Header Marks to a different character__

" Clear the existing syntax definition for the header marker
syntax clear pandocAtxStart

" Re-define pandocAtxStart to match the header marker (#), and conceal it with an 'O'
syn match pandocAtxStart /#/ contained containedin=pandocAtxHeaderMark conceal cchar=O

__Mark the Level of a Heading with a Letter__

If you want to mark the header level with a letter, this would be the solution:

```vim
syn match pandocAtxStart1 /^#\s/ contained containedin=pandocAtxHeaderMark conceal cchar=A
syn match pandocAtxStart2 /^##\s/ contained containedin=pandocAtxHeaderMark conceal cchar=B
syn match pandocAtxStart3 /^###\s/ contained containedin=pandocAtxHeaderMark conceal cchar=C
syn match pandocAtxStart4 /^####\s/ contained containedin=pandocAtxHeaderMark conceal cchar=D
syn match pandocAtxStart5 /^#####\s/ contained containedin=pandocAtxHeaderMark conceal cchar=E
syn match pandocAtxStart6 /^######\s/ contained containedin=pandocAtxHeaderMark conceal cchar=F
```

__Hiding the Header Marks__

In case you would like to hide the `#` marks altogether, then you can try it
with `conceal`. Omit the `cchar` entirely: If you define a syntax item with
just the conceal flag and set your Vim option `conceallevel` to 2, then the
concealed text won’t be shown at all.

```vim
set conceallevel=2
syn match pandocAtxStart /#/ contained containedin=pandocAtxHeaderMark conceal
```

This will hide all '#' marks and the space between the marks and the heading.

__Underline the Heading__

In the good old time of the typewriter it was possible to understand a heading
by the fact that it had an underline. With the standard vim syntax highlighting
it is possible to do that. In addition we make all heading yellow (high
contrast to black) and bold so they really stand out.

```vim
" Combine red, bold, and underline for ATX-style headers
highlight pandocAtxHeader guifg=#FF0000 gui=bold,underline ctermfg=Yellow cterm=bold,underline

" Combine red, bold, and underline for Setex-style headers
highlight pandocSetexHeader guifg=#FF0000 gui=bold,underline ctermfg=Yellow cterm=bold,underline
```

__Removing the Gray Conceal from the Bullet Point__

The hyphen used as a bullet point gets replaced by a concealed bullet point.
While the hyphen is yellow and well visible the white bullet point on the gray
background is not. So how to not used a concealed char in this case? Look at
the definition of `pandocUListItemBullet` in `~/vim-syntax.txt` and create an
entry like this:

```vim
" Clear the existing definition so that any conceal attribute is removed
syntax clear pandocUListItemBullet

" Re-define it without the conceal flag so that the original character is shown
syn match pandocUListItemBullet /^>\=\s*\zs[*+-]/ contained containedin=pandocUListItem
```

## Everything Together

```vim
set conceallevel=2
syn match pandocAtxStart /^#\+\s/ contained containedin=pandocAtxHeaderMark conceal

highlight pandocAtxHeader guifg=#FF0000 gui=bold,underline ctermfg=Yellow cterm=bold,underline
highlight pandocSetexHeader guifg=#FF0000 gui=bold,underline ctermfg=Yellow cterm=bold,underline

syntax clear pandocUListItemBullet
syn match pandocUListItemBullet /^>\=\s*\zs[*+-]/ contained containedin=pandocUListItem
```




