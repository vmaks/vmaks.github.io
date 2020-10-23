---
layout: post
title:  "How to turn off CORS for a Spring server application"
date:   2020-06-14 19:26:00 +0300
categories: other
---

### Overview

[Cross-Origin Resource Sharing (CORS)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) is a mechanism that uses additional HTTP headers to tell browsers to give a web application running at one origin, access to selected resources from a different origin.
All modern browsers support it.
Nowadays, most web pages are single-page applications [(SPAs)](https://en.wikipedia.org/wiki/Single-page_application). 
It means that you can run a frontend application separately from the backend application in debug mode. 
CORS prevents you from this. 
In this case, you may want to temporarily turn it off.
In the post, we are going to discuss how to turn off CORS for a Spring server application.

### Turn off CORS

If you don't use Spring security then you could [easily turn off CORS](https://stackoverflow.com/a/35092082/3001953).

Simply add the following code:

```
/*
* Dissable CORS
*/
@Profile("dev")
@Configuration
@EnableWebMvc
public class WebConfig extends WebMvcConfigurerAdapter {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**");
    }
}
```

I also recommend using [Spring Profiles](https://docs.spring.io/spring-boot/docs/2.3.x/reference/html/spring-boot-features.html#boot-features-profiles) to easily enable and disable it.

### Conclusion

We have discussed how to turn off CORS for a Spring server application.
