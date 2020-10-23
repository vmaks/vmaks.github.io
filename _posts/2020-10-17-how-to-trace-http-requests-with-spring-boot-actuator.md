---
layout: post
title:  "How to trace http requests with the help of spring boot actuator"
date:   2020-10-17 21:13:00 +0300
categories: other
---

### Overview

In the post, we are going to trace http requests with the help of a *spring boot actuator*.

### Tracing http requests with the help of spring boot actuator

Firstly, add *spring-boot-starter-actuator* dependency to the pom.xml file:

```
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Secondly, enable HTTP tracing by adding [the following Configuration bean](https://stackoverflow.com/a/59115579/3001953) to the project:

```
@Configuration
public class HttpTraceActuatorConfiguration {

    @Bean
    public HttpTraceRepository httpTraceRepository() {
        return new InMemoryHttpTraceRepository();
    }

}
```

We have to do it because [Actuator HTTP Trace and Auditing features are not enabled by default anymore in Spring-Boot-2.2.0](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.2.0-M3-Release-Notes#actuator-http-trace-and-auditing-are-disabled-by-default).

Thirdly, Add the following lines to the application.properties:

```
management.endpoints.web.exposure.include=httptrace
management.endpoints.enabled-by-default=true
```

or in yml format:

```
management:
  endpoints:
    web:
      exposure:
        include: "httptrace"
    enabled-by-default: true
```

You could find all available endpoints [here](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints).

Lastly, you could check out out the last 100 http requests by the following link:
http://localhost:8080/actuator/httptrace 

Replace hostname and port if needed.  

### Conclusion

We have described how to trace http requests with the help of a *spring boot actuator*.
