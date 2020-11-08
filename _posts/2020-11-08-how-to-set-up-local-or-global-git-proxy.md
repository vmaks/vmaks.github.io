---
layout: post
title:  "How to set up local or global git proxy"
date:   2020-11-08 18:05:00 +0300
tags: [Git]
---

### Overview

In the post, we are going to describe how to set up a local or [global](https://git-scm.com/docs/git-config#Documentation/git-config.txt---global) git proxy.

### Global proxy set up

You could either use the following command to set up a global git proxy:

```
git config --global http.proxy http://PROXY_IP:PROXY_PORT
```

or configure it manually by editing a file *~/.gitconfig*:

```
[http]
    proxy = PROXY_IP:PROXY_PORT
```

In Windows *~/.gitconfig* usually corresponds to *C:/Users/USER_NAME/.gitconfig*.

You can verify your configuration by [the following command](https://git-scm.com/docs/git-config#Documentation/git-config.txt---get):

```
git config --global --get http.proxy
```

### Local proxy set up

You could either use the following command when you clone repository to set up local git proxy:

```
git clone <REPOSITORY_URL> --config "http.proxy=PROXY_HOST:PROXY_PORT"
```

or configure it manually by editing a file *.git/config*:

```
[http]
    proxy = PROXY_HOST:PROXY_PORT
```

You can verify your configuration by [the following command](https://git-scm.com/docs/git-config#Documentation/git-config.txt---get):

```
git config --get http.proxy
```

### Conclusion

We have described how to set up local or global git proxy and verify them.
