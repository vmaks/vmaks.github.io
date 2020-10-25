---
layout: post
title:  "Why you should use Spring Boot Dev Tools"
date:   2020-02-24 17:33:00 +0300
tags: [Spring, IDE]
---

### Overview

[Spring Boot Dev Tools](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-devtools) is a great tool that helps you to develop an application faster. 
It reflects changes in the code without the need to restart the whole application each time.
In the post, we are going to discuss why and how to use Spring Boot Dev Tools.

### How to enable

To enable Spring Boot Dev Tools simply add the following dependency in the pom file:

```
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>
```
[Flagging the dependency as optional](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-devtools) in Maven or using the developmentOnly configuration in Gradle (as shown above) prevents devtools from being transitively applied to other modules that use your project.

[Repackaged archives do not contain devtools by default](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-devtools). 
You may want to include it. 
Using the Maven plugin set the *excludeDevtools* property to false:

```
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <excludeDevtools>false</excludeDevtools>
            </configuration>
        </plugin>
    </plugins>
</build>
```

Run an application in debug mode.
Then you could change code (change method, add method or class).
After that, if you want to reflect changes in the code simply build the project again:

![]({{ site.url }}/assets/2020-02-24-spring-boot-devtools-1.png)

Spring Boot Dev Tools will automatically restart the application.

### How does it work

[Spring Boot Dev Tools uses two classloaders](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-devtools-restart). 
The first or so-called base classloader loads classes that don't change (third party libraries).
The second or so-called restart classloader loads classes that change a lot (you classes).
When Spring Boot Dev Tools restart the application it throws away and created a new restart classloader.
It requires less time compared to the full application restart because it doesn't restart the base classloader.

### Conclusion

We have described why and how to use Spring Boot Dev Tools.
