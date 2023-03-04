---
title: Presentations Atop The ANSI Terminal
linkTitle: Patat
author: Christian Külker
date: 2023-03-04
version: 0.1.0
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Terminal
- Presentation
commands:
- patat
tags:
- Patat
- Haskell
- Markdown
Description:
   Presentations Atop The ANSI Terminal (patat) is a small tool that allows you
   to show presentations using only an ANSI terminal.

---

## Installation

This will install a binary without many dependencies.

```bash
aptitude install patat
```

## Usage

Write a markdown document with a YAML header like this:

```markdown
---
title: Patat
author: Christian Külker
...

# Patat

Presentations Atop The ANSI Terminal (patat) is a small tool that allows you to
show presentations using only an ANSI terminal.

# Installation

Patat can be installed using the package manager. On Debian use `aptitude`.

    aptitude install patat

```

## Advanced Usage

Open 2 terminals. In one terminal open an editor on a presentation, like `vim
presentation.md`, and in the second terminal open the presentation `patat
--watch presentation.md`. Changes will now be visible after saving.

## Configuration

Patat can be configured via `$HOME/.patat.yaml` and within the presentation
itself via the YAML metadata header.

For example, if you want the presentation to advance to the next slide every 10
seconds, you would add:

```yaml
patat:
    autoAdvanceDelay: 10
```

## Documentation

The information in this document is short, and there are many more advanced
methods available: advanced slide splitting, syntax highlighting, images, line
wrapping, margins, fragmented slides, pandoc extensions. See either the man
page on Debian Stretch or the <https://github.com> page.

While `man patat` on Debian 9 Stretch and 10 Buster gives a complete man page,
on Debian 11 Bullseye it only gives the advice to look at `pandoc`, which is
clearly a step backwards.

The github `README.md` file <https://github.com/jaspervdj/patat> will have
additional information about patat and its configuration, similar to the man
page.

## Quirks

- Note that `patat` is picky about the filename extension. For example, a file
  `test.markdown` will be treated as `Unknown file extension: ".markdown"`. Use
  `*.md` instead. This applies to older `patat` versions.  In the latest
  version `0.8.8.0`, which is currently not in stable Debian, the following
  extensions are valid: `.markdown`, `.md` , `.mdown`, `.mdtext`, `.mdtxt`,
  `.mdwn`, `.mkd`, `.mkdn`. The `.lhs` and `.org` extensions are handled
  differently.

- Make sure that the YAML header starts with `---` and ends with `...`.

- Since `patat` uses pandoc, the input format can be Pandoc Markdown or any
  other pandoc suppored Markdown format.

## Receipts

### Use Quick Guide as Slides

Documents in _Quick-Guide-En-US_ are written with the maximum heading level of
2 because the first level is used for the title. Feeding this into `patat` will
result in a presentation with single page presentation on Debian Stretch.
On Debian 10 Buster and Debian 11 Bullseye, `patat` automatically detects the
highest heading level and will use the level 2 headings for a slide border,
resulting in a multi-page presentation.

If you wanted to make level 2 the default slide page divider explicitly, you
could add the `slideLevel` key to your configuration, either globally or in the
case of `patat.md` it was added via the front matter `YAML`.

```yaml
patat:
    slideLevel: 2
```

This does not work with `patat 0.4.7.1`. This feature was added in `patat
5.0.0` and successfully tested on `patat 0.8.6.1`.

As mentioned in the first paragraph, the highest heading level is level 2, as
this is the case with _Quick-Guide_, this configuration is actually not needed
as long as the autodetection works, which it should by default.

## Source

- <https://github.com/jaspervdj/patat>

## Debian

| #  | Debian   | patat   | pandoc  |
| -- | -------- | ------- | ------- |
| 9  | Stretch  | 0.4.7.1 |         |
| 10 | Buster   | 0.8.2.1 |         |
| 11 | Bullseye | 0.8.6.1 | 2.9.2.1 |

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2023-03-04 | Initial release                                      |


[yaml]: http://yaml.org/
[front-matter]: http://pandoc.org/MANUAL.html#extension-yaml_metadata_block
