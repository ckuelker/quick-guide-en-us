---
title: Favicon.ico
linkTitle: Favicon.ico
author: Christian Külker
date: 2023-03-24
version: 0.1.2
type: doc
disclaimer: True
toc: True
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

The little icon in a browser's address bar is called [favicon.ico] and was
invented by Microsoft Corporation in 1999, according to Wikipedia. The favicon
was standardized by the _World Wide Web Consortium_ (W3C) in the HTML 4.01
recommendation. The icon is a small graphic file, and __ICO__, __PNG__, and
__GIF__ are widely supported formats. Besides Internet Explorer(tm), __SVG__ is
also supported by many browsers. In contrast to the other formats, an `.ico`
file is a __container__ for multiple icons of different sizes and formats
(`.bmp`, `.png`, ...).

## Usage

Depending on the favicon type (`.ico` or `.svg`) the reference in HTML is
different. In the case of an `.ico`, the icon is referenced by a `link element`
in the `<head>` section of an HTML page with `rel="shortcut icon"`, as the
`.ico` format is known.

```html
<link rel="shortcut icon" href="https://example.com/name.ico">
```

In the case of a `.svg` file, a `type` statement must be added.

```html
<link rel="icon" type="image/svg+xml" href="https://example.com/name.svg">
```

An alternative is to place an `.ico` file in the document root of the web
server that can serve it, and let the browser decide if it wants a favicon or
not.

Theoretically, the advantage of `.svg` is that this format scales to all sizes,
depending on the target browser. The next best option is `.ico` as it can
provide many pre-defined formats and sizes. In some cases `.ico` might be
better if the target cannot handle `.svg`. However, unlike in the year 1999,
the variety of targets is much greater today, and it is difficult to impossible
to know all target capabilities.

## Size

Favicons can come in many different sizes and are used for many different
purposes: even as desktop icons on some operating systems. The [best practice]
according to <https://stackoverflow.com> is:

* ICO favicon.ico (32x32)
* PNG favicon.png (96x96)
* Tile Icon tileicon.png (144x144)
* Apple Touch Icon apple-touch-icon-precomposed.png (152x152)

More information can be found at <https://github.com> via the [favicon cheat
sheet].

## Creation

The __ImageMagick__ command line tool `convert` can create an `.ico` file from
existing image files that are not necessarily `.png` files, like this:

```bash
convert favicon-16.bmp favicon-32.bmp favicon.ico
```

If you prefer __SVG__ over some static format as source format: create a
__square__ `.svg` file with `inkscape` and export it as `.png`. These steps can
be skipped if you already have  a master `.png` file or if you have icons of
different sizes.

Resize a master `.png` file with __ImageMagick__ and create one file for each
size:

```bash
convert master.png -resize 16x16 16.png
convert master.png -resize 32x32 32.png
convert master.png -resize 48x48 48.png
```

This will create `16.png`, `32.png` and `48.png`. Convert the `.png` files to
__ICO__ (add the files as icons to the container):

```bash
convert 16.png 32.png 48.png icon.ico
```

Make sure the `.ico` container contains everything:

```bash
identify favicon.ico
icon.ico[1] ICO 16x16 16x16+0+0 32-bit sRGB 21.2KB 0.000u 0:00.000
icon.ico[0] ICO 32x32 32x32+0+0 32-bit sRGB 21.2KB 0.000u 0:00.000
icon.ico[0] ICO 48x48 48x48+0+0 32-bit sRGB 21.2KB 0.000u 0:00.000
```

It might get a little tricky, but __ImageMagick__ could be used to do all these
steps in one go (after the `.svg` file is created with `inkscape`).

```bash
convert -background none icon.svg -define icon:auto-resize favicon.ico
```

In some cases:

```bash
convert -density 256x256 -background transparent favicon.svg \
-define icon:auto-resize -colors 256 favicon.ico
```

If the density parameter is needed, it depends on the `.svg` file. For
example:

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
| 0.1.2   | 2023-03-24 | Improve writing, rewrite some parts, typos, fixes    |
| 0.1.1   | 2022-07-08 | History, shell to bash, formatting                   |
| 0.1.0   | 2020-05-11 | Initial release                                      |

[favicon.ico]: https://en.wikipedia.org/wiki/Favicon
[best practice]: https://graphicdesign.stackexchange.com/questions/26946/favicon-best-practices-regarding-size-and-format
[favicon cheat sheet]: https://github.com/audreyr/favicon-cheat-sheet

