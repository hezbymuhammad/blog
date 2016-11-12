---
layout: post
title: Change Markdown Parser from Redcarpet to Kramdown
description: "Starting May 1st, 2016, Github only support Kramdown as Markdown parser. This post is about what you should done on updating Redcarpet to Kramdown."
modified: 2016-11-12
tags: [markdown, tutorial, troubleshoot, tips, experience, bug, update]
share: true
---

## Why Change?

Redcarpet and Pygments is a widely used markdown parser and highlighter. I have already use it for a long time. Recently, i received many warning email from github regarding of the change between Redcarpet to Kramdown. [Starting May 1st, 2016](https://github.com/blog/2100-github-pages-now-faster-and-simpler-with-jekyll-3-0), Github only support Kramdown as Markdown parser and Rouge as highlighter. It stated that the use of Kramdown and Rouge boost it performance.

## The Problem

It turns out that changing between Redcarpet and Pygments to Kramdown and Rouge is not that simple. You cannot just change ```_config.yml```. There is some issue that should be fixed afterwards.

## Resolve
In order to get rid of the problem, one should simply follow these steps.

### Modify ```_config.yml```

First you must modify your ```_config.yml```. Change following lines

```yaml
   highlighter: pygments
   markdown: redcarpet
```

to

```yaml
   highlighter: rouge
   markdown: kramdown
   kramdown:
      input: GFM
      auto_ids: true
      syntax_highlighter: rouge
```

### Install Dependency

Dont forget to add following lines to your gemfile.

```yaml
   gem 'rouge'
   gem 'kramdown'
```

### Update

After all those two steps complete, run

    bundle install
    bundle update

## My Issue

After update, i faced jekyll-pagination issue. It should simply resolved by modifying following lines on your ```_config.yml```

```yaml
gems:
  - jekyll-sitemap
```

to

```yaml
gems:
  - jekyll-sitemap
  - jekyll-paginate
```