---
layout: post
title:  "How to use @Transactional with multiple databases and Spring Boot"
date:   2020-10-01 22:42:00 +0300
tags: [Spring, Spring Data, Kotlin]
---

### Overview

Sometimes you may need to use multiple databases in a Spring Boot application. 
You could read about how to set up multiple databases in a Spring Boot application in [the previous post](https://vmaks.github.io/other/2020/02/02/how-to-set-up-multiple-databases-with-spring-boot.html). 
In the post, we are going to describe how to use @Transactional with multiple databases in a spring boot application.

### The usage of @Transactional with multiple databases

Firstly, we need to import *@Transactional* annotation from the following module:

```
import org.springframework.transaction.annotation.Transactional 
```

Secondly, we have to use the Qualifier name to specify the transactional manager for a transaction:

```
@Transactional(FIRST_TRANSACTIONAL_MANAGER_QUALIFIER)
fun first() {
    ...
}

@Transactional(SECOND_TRANSACTIONAL_MANAGER_QUALIFIER)
fun second() {
    ...
}

```

If you use *@Primary* annotation on a first transactional manager then you don't have to set FIRST_TRANSACTIONAL_MANAGER_QUALIFIER in *@Transactional*. It will be used by default.

### Conclusion

We have described how to use @Transactional with multiple databases in a spring boot application.
