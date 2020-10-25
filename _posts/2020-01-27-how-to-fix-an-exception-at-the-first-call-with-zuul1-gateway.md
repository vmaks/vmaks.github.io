---
layout: post
title:  "How to fix an exception at the first call with Zuul1 gateway"
date:   2020-01-27 23:45:00 +0300
tags: [Spring, Spring Cloud]
---

### Overview

[Zuul](https://github.com/Netflix/zuul) is the first thing that comes to mind if you need to use a gateway with the spring framework.
However, you will get an exception at the first call with default settings.
In the post, we are going to discuss how to fix an exception at the first call with the Zuul1 gateway.

### ZuulException and how to fix it

You will get the following exception on the first request:

```
com.netflix.zuul.exception.ZuulException:
at org.springframework.cloud.netflix.zuul.filters.post.SendErrorFilter.findZuulException(SendErrorFilter.java:118...2.2.0.RC1]
at org.springframework.cloud.netflix.zuul.filters.post.SendErrorFilter.run(SendErrorFilter.java:78) ~[spring-clou...2.2.0.RC1]
```

To fix it you need to [increase the timeout for the ribbon client and enable eager-load (optionally)](https://stackoverflow.com/questions/55084722/zuulexception-senderrorfilter-at-first-call/55229628#55229628).

### Increase timeout

Simply add the following parameters in the application.properties file:

```
hystrix.command.default.execution.isolation.thread.timeoutInMilliseconds=11000

ribbon.ConnectTimeout=10000
ribbon.ReadTimeout: 10000
```

You may tweak the numbers.

### Enable eager-load (optionally)

You may also enable eager-loading to update the list of services on startup.

It will help in the following case: the gateway will be load at the end (all services have been initialized).

Add the following parameters in the application.properties file:

```
zuul.ribbon.eager-load.enabled= true
```

### Conclusion

We have described how to fix an exception at the first call with the Zuul1 gateway.
