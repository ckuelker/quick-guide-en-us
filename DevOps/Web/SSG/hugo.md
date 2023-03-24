---
title: Hugo
author: Christian Külker
date: 2023-03-24
version: 0.1.1
tags:       [Hugo, Go]
categories:
  - SSG
  - CMS
toc: True
locale: en_US
lang: en
type: doc
disclaimer: True
tags:
- Hugo
description: Hugo is an open source static site generator

---

## Introduction

Written in Go, Hugo is an open source static website generator (SSG) available
under the Apache License 2.0. Hugo supports TOML, YAML, and JSON data file
types, Markdown and HTML content files, and uses short-codes to add rich
content. Other notable features include taxonomies, multi-language mode, image
processing, custom output formats, HTML/CSS/JS minification, and support for
Sass SCSS workflows.

## Documentation

- <https://xyproto.github.io/splash/docs/all.html>
- <https://xyproto.github.io/splash/docs/longer/all.html>

## Installation Hugo 0.64.1 Debian 10

This installation was tested on Debian 10 Buster. It clones a specific version
from the upstream git repository.

###  Dependencies

* minimal `golang` 1.11 is needed

```bash
aptitude install golang
```

### Fetch Source Code

```bash
export URL=https://github.com/gohugoio/hugo.git
export VER=0.64.1
mkdir ~/g/github.com/gohugoio/hugo-0.64.1
cd ~/g/github.com/gohugoio/
git clone --branch v$VER --single-branch --depth 1  $URL hugo-$VER
```

This fetches just 72 MB instead of 177 MB for a complete clone. However this
clone is in a `detached HEAD mode`; without tagging, no commits can be made,
which should not be a problem.

### Hugo and Go Environment

As the self compiled version of hugo, the `PATH` should be set. This is a
suggestion for an environment, located at `/srv/env/hugo-0.64.1`.

```bash
export ENV_HUGO_NS=hugo-0.64.1
export ENV_HUGO_ROOT=$HOME/g/github.com/gohugoio/hugo-0.64.1
export PATH=$ENV_HUGO_ROOT:$PATH
```

If Go is installed via the package manager, no special environment is
needed. In this example a self compiled version of Go was used. The following
is a suggestion for the Go environment, located at `/srv/env/go-v1.13.7`. Two
variables are important: `GOROOT` and `GOPATH`.

```bash
export ENV_NODE_NS=go-v1.13.7
export GOROOT=$HOME/g/github.com/golang/go1.13.7
export GOPATH=/srv/go
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
```

### Build Building Hugo

The build time of `hugo` depends on the state of download. If everything is
downloaded it takes about 2 minutes. So this would __not__ work on an
isolated machine without network connection.

```bash
cd  hugo-0.64.1
source  /srv/env/go-v1.13.7
go version
go version go1.13.7 linux/amd64
go build

./hugo version
Hugo Static Site Generator v0.64.1 linux/amd64 BuildDate: unknown
```

## {FAILED} Build Building Hugo Extended [Debian 10 Buster]

The extended version of hugo is needed for some themes to support TOCSS.

```bash
cd  hugo-0.64.1
source  /srv/env/go-v1.13.7
go version
go version go1.13.7 linux/amd64
go build --tags extended
# github.com/wellington/go-libsass/libs
/tmp/pkg/mod/github.com/wellington/go-libsass@v0.9.3-0.20181113175235-c6364420\
6701/libs/sass_number.go:8:9: undefined: SassNumber
/tmp/pkg/mod/github.com/wellington/go-libsass@v0.9.3-0.20181113175235-c6364420\
6701/libs/sass_number.go:151:10: undefined: SassNumber
/tmp/pkg/mod/github.com/wellington/go-libsass@v0.9.3-0.20181113175235-c6364420\
6701/libs/sass_number.go:153:9: undefined: SassNumber
/tmp/pkg/mod/github.com/wellington/go-libsass@v0.9.3-0.20181113175235-c6364420\
6701/libs/sass_number.go:157:10: undefined: SassNumber
/tmp/pkg/mod/github.com/wellington/go-libsass@v0.9.3-0.20181113175235-c6364420\
6701/libs/sass_number.go:159:9: undefined: SassNumber
/tmp/pkg/mod/github.com/wellington/go-libsass@v0.9.3-0.20181113175235-c6364420\
6701/libs/sass_number.go:164:10: undefined: SassNumber
/tmp/pkg/mod/github.com/wellington/go-libsass@v0.9.3-0.20181113175235-c6364420\
6701/libs/sass_number.go:166:9: undefined: SassNumber
/tmp/pkg/mod/github.com/wellington/go-libsass@v0.9.3-0.20181113175235-c6364420\
6701/libs/sass_number.go:170:10: undefined: SassNumber
/tmp/pkg/mod/github.com/wellington/go-libsass@v0.9.3-0.20181113175235-c6364420\
6701/libs/sass_number.go:175:28: undefined: SassNumber
/tmp/pkg/mod/github.com/wellington/go-libsass@v0.9.3-0.20181113175235-c6364420\
6701/libs/sass_number.go:186:24: undefined: SassNumber
/tmp/pkg/mod/github.com/wellington/go-libsass@v0.9.3-0.20181113175235-c6364420\
6701/libs/sass_number.go:166:9: too many errors
```

Build downloads 480MB and takes 1 minute for compile time

## Extended Pre-compiled Hugo [Debian 10 Buster]

```bash
cd /srv/src
wget https://github.com/gohugoio/hugo/releases/download/v0.64.1/hugo_extended\
_0.64.1_Linux-64bit.tar.gz
cd /srv/build
mkdir hugo_extended_0.64.1
cd hugo_extended_0.64.1
tar xvzf ../../src/hugo_extended_0.64.1_Linux-64bit.tar.gz

source /srv/env/go-v1.13.7
source /srv/env/hugo-extended-0.64.1
hugo version
Hugo Static Site Generator v0.64.1-C327E75D/extended linux/amd64 BuildDate:\
 2020-02-09T20:51:43Z
```

## Testing docsy Theme

```bash
cd ~/scratch/hugo-0.64.1-new-site-docsy/themes

git clone --recursive

OR (after cloning)
cd docsy
git submodule update --init

OR (after cloning, and expecting nested stuff)
cd docsy
git submodule update --init --recursive

Submodule 'assets/vendor/Font-Awesome' (https://github.com/FortAwesome/Font-Aw\
esome.git) registered for path 'assets/vendor/Font-Awesome'
Submodule 'assets/vendor/bootstrap' (https://github.com/twbs/bootstrap.git) re\
gistered for path 'assets/vendor/bootstrap'
Cloning into '$HOME/scratch/hugo-0.64.1-new-site-docsy/themes/docsy/assets/v\
endor/Font-Awesome'...
Cloning into '$HOME/scratch/hugo-0.64.1-new-site-docsy/themes/docsy/assets/v\
endor/bootstrap'...
Submodule path 'assets/vendor/Font-Awesome': checked out '538a5bc4cf7989821d05\
2e3ff919e2bead07021c'
Submodule path 'assets/vendor/bootstrap': checked out '3b558734382ce58b51e5fc6\
76453bfd53bba9201'

source /srv/env/node-v12.5.0-linux-x64
# -D, --save-dev: Package will appear in your devDependencies.
npm install -D --save autoprefixer
...
+ autoprefixer@9.7.4
npm install -D --save postcss-cli
...
+ postcss-cli@7.1.0
```

If you omit npm installations, you will encounter this error.

```bash
cd ~/scratch/hugo-0.64.1-new-site-docsy
hugo
Building sites … ERROR 2020/02/14 23:40:20 Transformation failed: SCSS process\
ing failed: file "stdin", line 6, col 1: File to import not found or unreadabl\
e: ../vendor/bootstrap/scss/bootstrap.
```

~~~
Building sites …
  Replace Autoprefixer browsers option to Browserslist config.
  Use browserslist key in package.json or .browserslistrc file.

  Using browsers option can cause errors. Browserslist config
  can be used for Babel, Autoprefixer, postcss-normalize and other tools.

  If you really need to use option, rename it to overrideBrowserslist.

  Learn more at:
  https://github.com/browserslist/browserslist#readme
  https://twitter.com/browserslist
~~~

## Testing Docsy Theme Example (Faster)

```bash
export URL=https://github.com/google/docsy-example.git
export DIR=hugo-0.64.1-new-site-docsy-example
git clone --recurse-submodules $URL $DIR
cd  $DIR
du -sch *
66M total
source /srv/env/go-v1.13.7
source /srv/env/hugo-extended-0.64.1
source /srv/env/node-v12.5.0-linux-x64
npm install -D --save autoprefixer
npm install -D --save postcss-cli
```

## Source Code Latest Version

### Get The Source

```bash
cd ~/g/github.com/
mkdir gohugoio
cd gohugoio
git clone https://github.com/gohugoio/hugo.git
```

### Building Hugo [Debian Buster]

takes some time, mega download, puts 884MB into ~/go !!!

=> https://gohugo.io/getting-started/installing

```bash
cd  hugo
go build

./hugo version
Hugo Static Site Generator v0.61.0-DEV linux/amd64 BuildDate: unknown
```

## Configuration

* [doc](https://gohugo.io/getting-started/configuration/#all-configuration-settings)

### boiler plate

```bash
$HOME/g/github.com/gohugoio/hugo/hugo new site /tmp/new-site

Congratulations! Your new Hugo site is created in /tmp/new-site.

Just a few more steps and you're ready to go:

1. Download a theme into the same-named folder.
   Choose a theme from https://themes.gohugo.io/ or
   create your own with the "hugo new theme <THEMENAME>" command.
2. Perhaps you want to add some content. You can add single files
   with "hugo new <SECTIONNAME>/<FILENAME>.<FORMAT>".
3. Start the built-in live server via "hugo server".

Visit https://gohugo.io/ for quickstart guide and full documentation.
```

### Themes

* [github](https://github.com/thingsym/hugo-theme-techdoc)

```bash
cd new-site/theme
git clone https://github.com/thingsym/hugo-theme-techdoc.git
cd new-site
echo 'theme = "hugo-theme-techdoc"' >> config.toml
```

Consider:

As a test installation the `Mainroad` theme is used. Seems well supported by
26 contributors and has a lot of features and supports many browsers

* [github](https://github.com/Vimux/Mainroad)
* [Example](https://www.shanestillwell.com/)

git clone https://github.com/vimux/mainroad
git submodule add https://github.com/vimux/mainroad

Consider (beautiful)

* [github](https://github.com/zwbetz-gh/papercss-hugo-theme)
* [demo](https://themes.gohugo.io//theme/papercss-hugo-theme/papercss-shortcodes/)

### Add New Page (Add Content)

```bash
$HOME/g/github.com/gohugoio/hugo/hugo new cms/hugo.md
/tmp/new-site/content/cms/hugo.md created
vim /tmp/new-site/content/cms/hugo.md
```

## Hugo Front Matter

See <https://gohugo.io/content-management/front-matter/>

### Hugo Markup

- <https://www.ii.com/hugo-markup-languages/>
- <https://freecontent.manning.com/using-markup-languages-with-hugo/>
- <https://gohugo.io/getting-started/configuration-markup/>

#### Frontmatter:

~~~
   ---
   markup: pandoc
   ---
~~~

#### config.toml

```toml
[markup]
  defaultMarkdownHandler = "goldmark"
  [markup.blackFriday]
    angledQuotes = false
    footnoteAnchorPrefix = ""
    footnoteReturnLinkContents = ""
    fractions = true
    hrefTargetBlank = false
    latexDashes = true
    nofollowLinks = false
    noreferrerLinks = false
    plainIDAnchors = true
    skipHTML = false
    smartDashes = true
    smartypants = true
    smartypantsQuotesNBSP = false
    taskLists = true
  [markup.goldmark]
    [markup.goldmark.extensions]
      definitionList = true
      footnote = true
      linkify = true
      strikethrough = true
      table = true
      taskList = true
      typographer = true
    [markup.goldmark.parser]
      attribute = true
      autoHeadingID = true
      autoHeadingIDType = "github"
    [markup.goldmark.renderer]
      hardWraps = false
      unsafe = false
      xHTML = false
  [markup.highlight]
    codeFences = true
    guessSyntax = false
    hl_Lines = ""
    lineNoStart = 1
    lineNos = false
    lineNumbersInTable = true
    noClasses = true
    style = "monokai"
    tabWidth = 4
  [markup.tableOfContents]
    endLevel = 3
    ordered = false
    startLevel = 2
```


## Usage

### Server Development Site

```bash
cd $HOME/scratch/hugo-new-site
$HOME/g/github.com/gohugoio/hugo/hugo server

Building sites … WARN 2019/12/02 12:15:53 Page.Hugo is deprecated and will be
removed in a future release. Use the global hugo function.

WARN 2019/12/02 12:15:53 Page.URL is deprecated and will be removed in a future
release. Use .Permalink or .RelPermalink. If what you want is the front matter
URL value, use .Params.url

                   | EN
+------------------+----+
  Pages            |  7
  Paginator pages  |  0
  Non-page files   |  0
  Static files     |  5
  Processed images |  0
  Aliases          |  0
  Sitemaps         |  1
  Cleaned          |  0

Built in 31 ms
Watching for changes in
/tmp/new-site/{archetypes,content,data,layouts,static,themes}
Watching for config changes in /tmp/new-site/config.toml
Environment: "development"
Serving pages from memory
Running in Fast Render Mode. For full rebuilds on change: hugo server
--disableFastRender
Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
Press Ctrl+C to stop
```

New pages are drafts and are NOT displayed. To actually display them use `-D`
option or `--buildDrafts` option!

```bash
$HOME/g/github.com/gohugoio/hugo/hugo server --enableGitInfo --i18n-warnings\
 --path-warnings --templateMetrics --verbose --debug -D
```

### Publish Content

    $HOME/g/github.com/gohugoio/hugo/hugo

Will write data to `pulic`

### Add Home Page

```bash
    cd /tmp/new-site/content
    vim _index.md
```

## Simple Deploy

```bash
    cd /tmp/new-site/
    $HOME/g/github.com/gohugoio/hugo/hugo  server
```

## Example Pages

* [hichdev.com](https://hitchdev.com/) hugo + Material Theme


## URLs

* [home](https://github.com/gohugoio/hugo)
* [github](https://github.com/gohugoio/hugo)


## Using Hugo 0.64.1

    source /srv/env/hugo-0.64.1
    hugo version
    Hugo Static Site Generator v0.64.1 linux/amd64 BuildDate: unknown

```bash
hugo new site $HOME/scratch/hugo-0.64.1-new-site
Congratulations! Your new Hugo site is created in $HOME/scratch/hugo-0.64.1-new-site.

Just a few more steps and you're ready to go:

1. Download a theme into the same-named folder.
   Choose a theme from https://themes.gohugo.io/ or
   create your own with the "hugo new theme <THEMENAME>" command.
2. Perhaps you want to add some content. You can add single files
   with "hugo new <SECTIONNAME>/<FILENAME>.<FORMAT>".
3. Start the built-in live server via "hugo server".

Visit https://gohugo.io/ for quickstart guide and full documentation.
```

## Installing Techdoc Theme

```bash
    mkdir -p ~/g/github.com/thingsym
    cd ~/g/github.com/thingsym
    git clone https://github.com/thingsym/hugo-theme-techdoc.git
    cp -a ~/g/github.com/thingsym/hugo-theme-techdoc $HOME/scratch/hugo-0.64.1-new-site/themes
```

## Installing A Theme Inside A Git Project (The Content) [NOT TESTED]

```bash
    cd ~/scratch/hugo-new-site
    git submodule add https://github.com/alex-shpak/hugo-book themes/book
```

## Translating A Theme (i18n)

```bash
cd hugo-site
mkdir i18n
echo "[imprint]" >> i18n/en.toml
echo 'other = "Imprint"' >> i18n/en.toml
echo "[imprint]" >> i18n/de.toml
echo 'other = "Impressum"' >> i18n/de.toml
```

Then use it as `{{ T "imprint" }}`


## Problems

Your rendered home page is blank: /index.html is zero-length
 * Did you specify a theme on the command-line or in your
   "config.toml" file?  (Current theme: "")
 * For more debugging information, run "hugo -v"

## Output Formats

A

```html
{{ range .AlternativeOutputFormats -}}
<link rel="{{ .Rel }}" type="{{ .MediaType.Type }}" href="{{ .Permalink | safeURL }}">
{{ end -}}
```

B

```html
{{ .RelPermalink }} > /that-page/
{{ with  .OutputFormats.Get "json" -}}
{{ .RelPermalink }} > /that-page/index.json
{{- end }}
```


## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2023-03-24 | Improve writing, history                             |
| 0.1.0   | 2020-02-12 | Initial release                                      |


