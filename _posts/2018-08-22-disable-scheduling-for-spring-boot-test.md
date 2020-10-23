---
layout: post
title:  "How to disable scheduling for a spring boot test"
date:   2018-08-22 22:56:00 +0300
categories: other
---

Add a separate configuration class with *@Profile(!test)*, *@EnableScheduling* annotations

```
@Profile(!test)
@Configuration
@EnableScheduling
public static class SpringConfiguration {}
```

Then either add the following line below in the test *application.properties* file to disable the spring scheduling for the *test* [profile][1] 

```
spring.profiles.active=test
```

or add the following annotation [*@ActiveProfiles*][2] to a test class

```
@ActiveProfiles("test")
```

[1]: https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-profiles.html
[2]: https://www.baeldung.com/spring-profiles
