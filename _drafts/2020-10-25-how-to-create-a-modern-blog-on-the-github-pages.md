---
layout: post
title:  "How to create a modern blog on the GitHub Pages"
date:   2020-10-25 20:55:00 +0300
tags: [GitHub Pages]
---

### Overview

In the post, we are going to discuss how to create a modern blog on the GitHub Pages.

### Create a modern blog

- Download repository https://github.com/Huxpro/huxpro.github.io
- Replace posts in _posts folder
- Add assets folder with pictures
- Configure _config.yml file
- Edit about info: _includes/about/en.md; 
comment "Chinese Version" in the about.html page;
comment content of /_includes/multilingual-sel.html page
- Make empty description for index.html, about.html pages
- Make CNAME file empty or edit it.
- Enable Rss in the _config.yml file
- Add to the page.html just belo "Short About" block "Subscribe email"
- Change "header-img" in the _config.yml file
- Remove "categories" from a post
- Add "tags" to a post

### Test blog locally

- Install ruby

For windows, you could download here https://rubyinstaller.org/downloads/

- Install the jekyll and bundler gems:

```
gem install jekyll bundler
```

- Install dependencies based on Gemfile file:

```
bundle install
```

- Run server locally:

```
bundle exec jekyll serve
```

- Browse to http://localhost:4000

### Conclusion

We have described how to create a modern blog on the GitHub Pages.
