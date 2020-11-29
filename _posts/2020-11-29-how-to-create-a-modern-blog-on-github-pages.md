---
layout: post
title:  "How to create a modern blog on GitHub Pages"
date:   2020-11-29 16:21:00 +0300
tags: [GitHub Pages]
---

### Overview

In the post, we are going to discuss how to create a modern blog on GitHub Pages with pageable, tags, the ability to search posts and subscribe to new posts as well.

### Create a modern blog

I recommend doing the following steps if you want to create a modern blog on GitHub Pages:

- You could find lots of GitHub Pages themes [here](https://jekyllrb.com/docs/themes/), but we are going to use [huxpro theme](https://huangxuan.me/) in the post.
- Download the following [repository](https://github.com/Huxpro/huxpro.github.io).
- Replace posts in *_posts* folder with your data or make it clear.
- Add *assets* folder with pictures if you have any.
- Configure *_config.yml* file.
- Edit about info: *_includes/about/en.md*; 
comment out "Chinese Version" in the *about.html* page;
comment out content of */_includes/multilingual-sel.html* page.
- Make empty description for *index.html*, *about.html* pages.
- Make *CNAME* file empty or edit it.
- Enable RSS in the *_config.yml* file.
- Add to the *page.html* just below the "Short About" block from [FeedBurner](https://feedburner.google.com/) if you want to allow subscribing to the blog.
Don't forget to enable RSS otherwise it won't work.
- Change *header-img* in the *_config.yml* file.
- Remove *categories* from the old posts if you have any.
- Add *tags* to the old posts if you have any, and you want to allow filter content based on tags.

### Conclusion

We have described how to create a modern blog on GitHub Pages.
