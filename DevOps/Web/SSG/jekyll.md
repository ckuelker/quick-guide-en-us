---
title: Jekyll
author: Christian Külker
version: 0.1.1
date: 2024-07-13
type: doc
locale: en_US
lang: en
disclaimer: True
toc: True
categories:
- SSG
commands:
- jekyll
- gem
- wget
- bundle
- git
tags:
- Jekyll
- Pandoc
discription: Jekyll is a flat-file HTML site generator written in ruby

---

## Abstract

Jekyll is a powerful, flat-file HTML site generator developed in Ruby, designed
to create static websites from plain text. As an open-source tool, Jekyll
allows the integration with version control systems, making it an good choice
for developers looking to maintain blog posts, documentation, or any web
content without the overhead of databases or extensive back-end coding. This
document provides an overview of Jekyll, from installation and configuration to
usage with a focus on Pandoc and themes. Most parts were tested under Debian 10
Buster.

## Introduction

Jekyll is a popular static site generator that transforms plain text into
websites. Originally developed by Tom Preston-Werner in 2008, Jekyll has become
a tool for bloggers, developers, and content creators who seek to
maintain their web presence without the complexities of traditional website
management.

This document mirrors the first attempts of using Jekyll, from installing,
setting up an environment to deploying a final site.

We'll start with the basics of installation from various sources and move on to
more advanced topics such as customization with themes and usage with Pandoc.

## Installation

### Dependencies [Debian 10 Buster]

    aptitude install gem
    aptitude install ruby-dev

### Install Jekyll from Gem

    gem install jekyll bundler

### Install Jekyll from Source

```bash
cd /srv/src
wget https://github.com/jekyll/jekyll/archive/v3.8.5.tar.gz
cd /srv/build
tar xvzf ../src/v3.8.5.tar.gz
cd v3.8.5/exe
./jekyll -v
/usr/lib/ruby/2.3.0/rubygems/core_ext/kernel_require.rb:55:in `require': \
cannot load such file -- pathutil (LoadError)
        from /usr/lib/ruby/2.3.0/rubygems/core_ext/kernel_require.rb:55:in \
        `require'
        from /srv/build/jekyll-3.8.5/lib/jekyll.rb:32:in `<top (required)>'
        from /usr/lib/ruby/2.3.0/rubygems/core_ext/kernel_require.rb:55:in \
        `require'
        from /usr/lib/ruby/2.3.0/rubygems/core_ext/kernel_require.rb:55:in \
        `require'
        from ./jekyll:8:in `<main>'
```

#### Troubleshooting

```bash
ERROR:  Error installing jekyll:
        ERROR: Failed to build gem native extension.
...
mkmf.rb can't find header files for ruby at /usr/lib/ruby/include/ruby.h
```

=> install ruby-dev:

```bash
aptitude install ruby-dev
```

### Test Installation

```bash
    jekyll -v
    jekyll 4.0.0
```

## Usage With 'jekyll new'

Unexpectedly, creating a new site as a user fails:

```bash
    jekyll new jekyll-new-site
```

~~~
Your user account isn't allowed to install to the system RubyGems.
  You can cancel this installation and run:

      bundle install --path vendor/bundle

  to install the gems into ./vendor/bundle/, or you can enter your password
  and install the bundled gems to RubyGems using sudo.

  Password:
~~~

CTRL-C

    cd jekyll-new-site
    bundle install --path vendor/bundle

This basically fetches and installs the same dependencies once again.

### Usage With 'bunlde exec'

```bash
cd jekyll-new-site
bundle exec jekyll serve
```

Go to <http://localhost:4000>

Strangely `jekyll serve` does __NOT__ work

## Configuration

Initial site configuration is done via `_config.yml`.

### Choosing A Theme

There are at least three sites that offer a Jekyll theme overview:

- JAMstack: <https://github.com/mfenner/jekyll-pandoc> unusable
- Jakyllthemes.org: <http://jekyllthemes.org/> unusable,
  better clone: <https://github.com/mattvh/jekyllthemes>
- Jakyllthemes.io: <https://jekyllthemes.io/> partly proprietary

### Installing A Theme

For installing there are three options:

1. Gem-based method
2. Remote Theme method
3. Copying all files to working directory

#### Gem Base Theme

It is possible to understand where the files for the theme are stored.

```bash
bundle info --path minima
/var/lib/gems/2.5.0/gems/minima-2.5.1
```

#### Install Theme [minimal-mistakes]

- minimal-mistakes web page: <https://github.com/mmistakes/minimal-mistakes>

```bash
cd jekyll-new-site
vim _config.yml
```

Add Line

~~~
plugins:
 ...
 - jekyll-include-cache
~~~

```bash
vim  jekyll-new-site/Gemsite
```

~~~
# 2019-12-03 ckuelker
gem "minimal-mistakes-jekyll"
~~~

```bash
bundle
vim _config.yml
```

```yaml
# Build settings
#theme: minima
theme: minimal-mistakes-jekyll
```

```bash
cd jekyll-new-site
aptitude bundle update
```

Files in `_posts` for example will be transferred (probably by bundle) to
the directory on the server.

```bash
_posts/2019-12-03-japanese.markdown ->
     _site/2019/12/03/japanese.html
      http://127.0.0.1:4000/2019/12/03/japanese.html
```

If there is a category given in the page header like so:

```yaml
    ---
    layout: post
    title:  "Welcome to Jekyll!"
    date:   2019-12-03 13:54:40 +0100
    categories: jekyll update
    ---
```

```bash
_posts/2019-12-03-welcome-to-jekyll.markdown ->
    _site/jekyll/update/2019/12/03/welcome-to-jekyll.html
    http://127.0.0.1:4000/jekyll/update/2019/12/03/welcome-to-jekyll.html
```

### Install Theme just-the-docs

- <https://github.com/pmarsceill/just-the-docs>

```bash
echo 'gem "just-the-docs"' >> Gemfile
vim _config.yaml
```

~~~
theme: just-the-docs
~~~

The `just-the-docs.gemspec` shows that this theme is not for Jekyll 4.0.0.

## First Page Content

Edit the preexisting `index.markdown` and add some content.

## Posts

The preexisting `_post` directory can be filled with Markdown files like
`2019-12-03-japanese.markdown` or `2019-12-03-welcome-to-jekyll.markdow`.

Possible followup: it is unknown how they automatically get linked on the front
page.

## Site-map

- Site-map without plugin
  <http://www.independent-software.com/generating-a-sitemap-xml-with-jekyll-without-a-plugin.htm>
  (partly tested)
- <http://davidensinger.com/2013/11/building-a-better-sitemap-xml-with-jekyll/>
  (untested)
- <https://mijingo.com/blog/building-a-sitemap-with-jekyll> (untested)
- <https://github.com/jekyll/jekyll-sitemap> (untested)

## Creating PDF

### Creating PDF for the whole project

You can generate a PDF from your Jekyll project. You do this by creating a web
version of your project that is printer friendly. You then use utility called
Prince to iterate through the pages and create a PDF from them. It works quite
well and gives you complete control to customize the PDF output through CSS,
including page directives and dynamic tags from Prince.

See:
<https://idratherbewriting.com/documentation-theme-jekyll/mydoc_generating_pdfs.html>

### Creating PDF for a page

<https://github.com/abeMedia/jekyll-pdf>

add

~~~
gem "jekyll-pdf"
~~~

```bash
bundle install
```

~~~
pdf: true
~~~

TO BE CONTINUED: leaves dependency chaos

## Pandoc

There seem to be more than one integration with pandoc.

a) jekyll-pandoc: <https://github.com/mfenner/jekyll-pandoc>

  2016, pandoc >=1.15 (will current 2.2.1 work?)

b) jekyll-pandoc-multiple-formats: <https://github.com/fauno/jekyll-pandoc-multiple-formats>

  2018 moved to

  - 0xacab.org: <https://0xacab.org/edsl/jekyll-pandoc-multiple-formats>
  - example page: <https://endefensadelsl.org/>, but also this last update was
    2018

c) jekyll-pandoc-plugin: <https://github.com/fauno/jekyll-pandoc-plugin>

   2016 - not longer maintained

### Installation (First try)

__a) Installation__

```bash
echo 'gem "jekyll-pandoc"' >> Gemfile
bundle update
vim _config.yml
```

~~~
    gems:
        - jekyll-pandoc
    markdown: Pandoc
~~~

Problem

    Invalid Markdown processor given: Pandoc

__b) Installation__

- See: <https://rubygems.org/gems/jekyll-pandoc-multiple-formats>

```bash
vim _config.yml
```

~~~
    gems: [ 'jekyll-pandoc-multiple-formats' ]
~~~

```bash
    bundle exec jekyll build
    bundle exec jekyll server --trace
```

Problem:

    Markdown processor: "pandoc" is not a valid Markdown processor.
                    Available processors are: kramdown

- See: <https://rubygems.org/gems/jekyll-pandoc-multiple-formats-jekyll34>

It seems a fork `jekyll-pandoc-multiple-formats-jekyll34`

- doc:
  <https://www.rubydoc.info/gems/jekyll-pandoc-multiple-formats-jekyll34/0.2.12>

### Installation (Second Try)

```bash
# As root
cd $HOME/scratch
jekyll new jekyll-new-site-pandoc
....
  Bundler: Bundle complete! 6 Gemfile dependencies, 30 gems now installed.
  Bundler: Use `bundle info [gemname]` to see where a bundled gem is installed.
  Bundler: Post-install message from i18n:
  Bundler:
  Bundler: HEADS UP! i18n 1.1 changed fallbacks to exclude default locale.
  Bundler: But that may break your application.
  Bundler:
  Bundler: If you are upgrading your Rails application from an older version \
           of Rails:
  Bundler:
  Bundler: Please check your Rails app for 'config.i18n.fallbacks = true'.
  Bundler: If you're using I18n (>= 1.1.0) and Rails (< 5.2.2), this should be
  Bundler: 'config.i18n.fallbacks = [I18n.default_locale]'.
  Bundler: If not, fallbacks will be broken in your app by I18n 1.1.x.
  Bundler:
  Bundler: If you are starting a NEW Rails application, you can ignore this notice.
  Bundler:
  Bundler: For more info see:
  Bundler: https://github.com/svenfuchs/i18n/releases/tag/v1.1.0Don't run \
           Bundler as root. Bundler can ask for sudo if it is needed, and \
           installing your bundle as root will break this
  Bundler: application for all non-root users on this machine.
```

Debian is not using `sudo` as Ubuntu ...

### Installion (Third Try)

```bash
gem install jekyll bundle
Fetching: jekyll-4.0.0.gem (100%)
ERROR:  While executing gem ... (Gem::FilePermissionError)
    You don't have write permissions for the /var/lib/gems/2.5.0 directory.
gem install --local jekyll bundle
ERROR:  Could not find a valid gem 'jekyll' (>= 0) in any repository
ERROR:  Could not find a valid gem 'bundle' (>= 0) in any repository
```

Well ...

```bash
gem install --user-install jekyll bundle
WARNING:  You don't have $HOME/.gem/ruby/2.5.0/bin in your PATH,
          gem executables will not run.
-------------------------------------------------------------------------------
Jekyll 4.0 comes with some major changes, notably:

* Our `link` tag now comes with the `relative_url` filter incorporated into it.
  You should no longer prepend `{{ site.baseurl }}` to `{% link foo.md %}`
  For further details: https://github.com/jekyll/jekyll/pull/6727

* Our `post_url` tag now comes with the `relative_url` filter incorporated
  into it.
  You shouldn't prepend `{{ site.baseurl }}` to `{% post_url 2019-03-27-hello %}`
  For further details: https://github.com/jekyll/jekyll/pull/7589

* Support for deprecated configuration options has been removed. We will no
  longer output a warning and gracefully assign their values to the newer
  counterparts internally.
-------------------------------------------------------------------------------
Successfully installed jekyll-4.0.0
Parsing documentation for jekyll-4.0.0
Installing ri documentation for jekyll-4.0.0
Done installing documentation for jekyll after 4 seconds
Fetching: bundle-0.0.1.gem (100%)
Successfully installed bundle-0.0.1
Parsing documentation for bundle-0.0.1
Installing ri documentation for bundle-0.0.1
Done installing documentation for bundle after 0 seconds
2 gems installed
```

```bash
source ~/g/g.c8i.org/env/env/ruby-2.5.0
which jekyll
$HOME/.gem/ruby/2.5.0/bin/jekyll
jekyll help
...
jekyll new jekyll-new-site-pandoc
...
  Bundler: Bundle complete! 6 Gemfile dependencies, 30 gems now installed.
  Bundler: Use `bundle info [gemname]` to see where a bundled gem is \
           installed.Following files may not be writable, so sudo is needed:
  Bundler: /usr/local/bin
  Bundler: /var/lib/gems/2.5.0
  Bundler: /var/lib/gems/2.5.0/build_info
  Bundler: /var/lib/gems/2.5.0/cache
  Bundler: /var/lib/gems/2.5.0/doc
  Bundler: /var/lib/gems/2.5.0/extensions
  Bundler: /var/lib/gems/2.5.0/gems
  Bundler: /var/lib/gems/2.5.0/specifications
New jekyll site installed in $HOME/scratch/jekyll-new-site-pandoc.
cd jekyll-new-site-pandoc
jekyll serve
Configuration file: $HOME/scratch/jekyll-new-site-pandoc/_config.yml
            Source: $HOME/scratch/jekyll-new-site-pandoc
       Destination: $HOME/scratch/jekyll-new-site-pandoc/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
       Jekyll Feed: Generating feed for posts
                    done in 0.774 seconds.
 Auto-regeneration: enabled for '$HOME/scratch/jekyll-new-site-pandoc'
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.
```

New Site works locally for a user (and not root): good!

### Installation With Docsy (Forth Try)

```bash
cd ~/scratch
git clone https://github.com/vsoch/docsy-jekyll.git  jekyll-new-site-docsy
cd jekyll-new-site-docsy
source /srv/env/ruby-2.5.0
```

Change `Gemfile`. Comment out `gh_pages` line add following line

~~~
gem "jekyll", "4.0.0"
~~~

Run

```bash
jekyyl serve
```

Open Browser at: <http://127.0.0.1:4000/docsy-jekyll/>

In case of building the static site, the site prefix is missing, do:

```bash
jekyyl build
cd _site
mkdir docsy-jekyll
mv * docsy-jekyll
```

Point browser to `http://SERVER/docsy-jekyll/` however links are not working

=> This theme is very specific, build only for a specific site and for Github
pages.

- Difficult to make own standalone projects.
- Some links are pointing nowhere.
- Difficult from moving it from `jekyll serve` to `nginx`.
- Document taxonomy/directory structure flatten to only 1 dimension
  - very long list page: not usable for doc navigation, side bar only manual
    and not included in dynamic content



## Jekyll Review

- need gem
- need to be installed by root
- fetches a lot of stuff from the internet (security)
- takes long time to compile
- fetches a lot of stuff from the internet twice
- takes again a lot of time to compile
- do not automatically reload like Hugo

## Jekyll with Staticman (Not Tested)

Staticman receives user-generated content and uploads it as data files to a
GitHub and/or GitLab repository. In practice, this allows to have dynamic
content (e.g. blog post comments) as part of a static website, as long as the
site automatically deploys on every push to GitHub and/or GitLab, as seen on
GitHub Pages, GitLab Pages, Netlify and others. (from
<https://github.com/eduardoboucas/staticman>)

- Home: <https://staticman.net/>
- Github: <https://github.com/eduardoboucas/staticman>

## Documentation

- Quick start: <https://jekyllrb.com/docs/>
- Step By Step: <https://jekyllrb.com/docs/step-by-step/01-setup/>

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2024-07-13 | Improve formatting, Introduction, Abstract           |
| 0.1.0   | 2019-12-08 | Initial release                                      |

