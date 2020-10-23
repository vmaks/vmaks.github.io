---
layout: post
title:  "How to create a scheduled method with spring boot"
date:   2020-08-09 15:18:00 +0300
categories: other
---

### Overview

Sometimes you need to create a method that will be called many times in the future. 
We usually call such a method as a scheduled method.
In the post, we are going to discuss how to create a scheduled method with spring boot.

### Scheduled method

Firstly, add [*@EnableScheduling*](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/annotation/EnableScheduling.html) to any @Configuration class to enable scheduling in the application. 
I recommend adding it to the class marked with @SpringBootApplication.

```
package org.maks.scheduledmethodexample

import org.springframework.boot.autoconfigure.SpringBootApplication
import org.springframework.boot.runApplication
import org.springframework.scheduling.annotation.EnableScheduling

@EnableScheduling
@SpringBootApplication
class ScheduledMethodExampleApplication

fun main(args: Array<String>) {
	runApplication<ScheduledMethodExampleApplication>(*args)
}
```

Secondly, implement a method. 
Then add [*@Scheduled(fixedRateString = "...")* or *@Scheduled(cron = "...")*](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/annotation/Scheduled.html) annotation to the method. 
The former allows you to execute the method at a fixed interval while the latter helps to set custom time intervals with the help of [Cron expressions](http://www.quartz-scheduler.org/documentation/quartz-2.3.0/tutorials/crontrigger.html).
You could use [Cron Expression Generator](https://www.freeformatter.com/cron-expression-generator-quartz.html) to generate proper Cron expression. 

```
package org.maks.scheduledmethodexample

import org.springframework.scheduling.annotation.Scheduled
import org.springframework.stereotype.Service

@Service
class ScheduledService {

    @Scheduled(fixedRate = 1000)
    fun run() {
        println("run() has been executed")
    }
}
```

You could even combine several *@Scheduled* with the help of [*@Schedules*](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/annotation/Schedules.html) annotation.

### Conclusion

We have discussed how to create a scheduled method with spring boot.
You could check out the source code [here](https://github.com/vmaks/scheduled-method-example).
