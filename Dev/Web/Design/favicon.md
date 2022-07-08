---
title: Favicon.ico
linkTitle: Favicon.ico
author: Christian Külker
date: 2022-07-08
version: 0.1.1
type: doc
disclaimer: True
TOC: True
categories:
- Web
- Design
tags:
- Favicon
- Inkscape
- Favicon
- favicon.ico
- SVG
- PNG
- ICO
- Microsoft Corporation
- World Wide Web Consortium
- W3C
- ImageMagick
- HTML
commands:
- convert
- identify
- magick
- inkscape
description: Favicon

---

The small icon in a browsers address bar is called [favicon.ico] and was
according to Wikipedia invented by the Microsoft Corporation in 1999.  The
favicon was standardized by the World Wide Web Consortium (W3C) in the HTML
4.01 recommendation. The icon is a small graphic file and **ICO**, **PNG** and
**GIF** are widely supported formats. Except Internet Explorer(tm) also **SVG**
is supported by many browsers. An `.ico` file is a container for multiple
`.bmp` or `.png` icons of different sizes.

## Usage

The icon is referenced via a `link element` in the `<head>` section of
an HTML page.

```html
<link rel="shortcut icon" href="https://example.com/name.ico">
```

Or for a **SVG** image add a type statement:

```html
<link rel="icon" type="image/svg+xml" href="https://example.com/name.svg">
```

An alternative is to place it at the document root of the web site.

## Size

Favicons can nowadays be in many different sizes and are used for very
different purposes: even as a desktop symbol by some operating systems. [Best
practice] according to <https://stackoverflow.com> is:

* ICO favicon.ico (32x32)
* PNG favicon.png (96x96)
* Tile Icon tileicon.png (144x144)
* Apple Touch Icon apple-touch-icon-precomposed.png (152x152)

More information can be found on <https://github.com> via the [favicon cheat sheet].

## Creation

If you prefer **SVG** over some static format: create a **square** ``.svg``
file with `inkscape` and export it as `.png`. This steps can be skipped if `.png`
files are already present.

Resize `.png` files with ImageMagick:

```bash
magick convert master.png -resize 16x16 16.png
magick convert master.png -resize 32x32 32.png
magick convert master.png -resize 48x48 48.png
```

Convert the `.png` files to **ICO**:

```bash
magick convert 16.png 32.png 48.png icon.ico
```

Make sure the `.ico` container contains everything:

```bash
identify favicon.ico
icon.ico[1] ICO 16x16 16x16+0+0 32-bit sRGB 21.2KB 0.000u 0:00.000
icon.ico[0] ICO 32x32 32x32+0+0 32-bit sRGB 21.2KB 0.000u 0:00.000
icon.ico[0] ICO 48x48 48x48+0+0 32-bit sRGB 21.2KB 0.000u 0:00.000
```

The **ImageMagick** command line tool `convert` can create an `.ico` file from
pre existing `.png` files, like so:

```bash
convert favicon-16.png favicon-32.png favicon.ico
```

It might get a little bit tricky, but **ImageMagick** could be use for all this
steps in one go (after the `.svg` file was created with `inkscape`)

```bash
convert -background none icon.svg -define icon:auto-resize favicon.ico
```

In some cases:

```bash
convert -density 256x256 -background transparent favicon.svg \
-define icon:auto-resize -colors 256 favicon.ico
```

If the density parameter is needed dependents on the `.svg` file.

This gives for example:

```bash
identify favicon.ico
favicon.ico[0] PNG 256x256 256x256+0+0 8-bit sRGB 300KB 0.000u 0:00.000
favicon.ico[1] ICO 192x192 192x192+0+0 32-bit sRGB 300KB 0.000u 0:00.000
favicon.ico[2] ICO 128x128 128x128+0+0 32-bit sRGB 300KB 0.000u 0:00.000
favicon.ico[3] ICO 96x96 96x96+0+0 32-bit sRGB 300KB 0.000u 0:00.000
favicon.ico[4] ICO 64x64 64x64+0+0 32-bit sRGB 300KB 0.000u 0:00.000
favicon.ico[5] ICO 48x48 48x48+0+0 32-bit sRGB 300KB 0.000u 0:00.000
favicon.ico[6] ICO 40x40 40x40+0+0 32-bit sRGB 300KB 0.000u 0:00.000
favicon.ico[7] ICO 32x32 32x32+0+0 32-bit sRGB 300KB 0.000u 0:00.000
favicon.ico[8] ICO 24x24 24x24+0+0 32-bit sRGB 300KB 0.000u 0:00.000
favicon.ico[9] ICO 16x16 16x16+0+0 32-bit sRGB 300KB 0.000u 0:00.000
```

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2022-07-08 | History, shell to bash, formating                    |
| 0.1.0   | 2020-05-11 | Initial release                                      |

[favicon.ico]: https://en.wikipedia.org/wiki/Favicon
[best practice]: https://graphicdesign.stackexchange.com/questions/26946/favicon-best-practices-regarding-size-and-format
[favicon cheat sheet]: https://github.com/audreyr/favicon-cheat-sheet

