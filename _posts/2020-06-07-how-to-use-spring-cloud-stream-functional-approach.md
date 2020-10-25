---
layout: post
title:  "How to use Spring Cloud Stream functional approach"
date:   2020-06-07 18:09:00 +0300
tags: [Spring, Spring Cloud, Kotlin]
---

### Overview

We are going to describe how to create an application with [Spring Cloud Stream](https://cloud.spring.io/spring-cloud-static/spring-cloud-stream/3.0.4.RELEASE/reference/html/index.html) [*functional*](https://cloud.spring.io/spring-cloud-static/spring-cloud-stream/3.0.4.RELEASE/reference/html/spring-cloud-stream.html#spring_cloud_function) approach.
Previously, we [reviewed](https://vmaks.github.io/other/2020/06/03/how-to-use-spring-cloud-stream-annotation-based-approach.html) how to use the old way or so-called [*annotation-based*](https://cloud.spring.io/spring-cloud-static/spring-cloud-stream/3.0.4.RELEASE/reference/html/spring-cloud-stream.html#_annotation_based_support_legacy) approach.

### Spring Cloud Stream Hello World

We are going to build a Spring application with Gradle build system, Kotlin, Spring Cloud Stream 3.0.4.RELEASE and use [topic exchange to route messages to the correspondent queue based on the routing key](https://www.rabbitmq.com/tutorials/tutorial-five-python.html) in the example.

Firstly, [create an application](https://start.spring.io/) with the following dependencies: Spring Web, Cloud Stream, Spring for RabbitMQ. 

```
package com.maks.springcloudstream

import org.springframework.boot.autoconfigure.SpringBootApplication
import org.springframework.boot.runApplication

@SpringBootApplication
class SpringCloudStreamApplication

fun main(args: Array<String>) {
	runApplication<SpringCloudStreamApplication>(*args)
}

```

Secondly, add MessageHandler class with two methods for sending and receiving messages.
You could find more information about how to implement methods [here](https://cloud.spring.io/spring-cloud-static/spring-cloud-stream/3.0.4.RELEASE/reference/html/spring-cloud-stream.html#spring-cloud-stream-overview-producing-consuming-messages).

```
package com.maks.springcloudstream

import org.slf4j.Logger
import org.slf4j.LoggerFactory
import org.springframework.context.annotation.Bean
import org.springframework.context.annotation.Configuration
import org.springframework.messaging.Message
import reactor.core.publisher.EmitterProcessor
import reactor.core.publisher.Flux
import java.util.function.Consumer
import java.util.function.Supplier

@Configuration
class MessageHandler {

    companion object {
        val processor: EmitterProcessor<Message<*>> = EmitterProcessor.create()
        val logger: Logger = LoggerFactory.getLogger(MessageHandler::class.java)
    }

    @Bean
    fun send(): Supplier<Flux<Message<*>>> {
        return Supplier {
            processor
        }
    }

    @Bean
    fun receive(): Consumer<String> {
        return Consumer<String> {
            logger.info(it)
        }
    }
}
```

Thirdly, we create a Controller with a method that sends a message into the output queue:

```
package com.maks.springcloudstream

import org.springframework.http.ResponseEntity
import org.springframework.messaging.Message
import org.springframework.messaging.support.MessageBuilder
import org.springframework.web.bind.annotation.PostMapping
import org.springframework.web.bind.annotation.RequestBody
import org.springframework.web.bind.annotation.RequestMapping
import org.springframework.web.bind.annotation.RestController
import reactor.core.publisher.EmitterProcessor

@RestController
@RequestMapping("/")
class MessageController {

    @PostMapping
    fun sendMessage(@RequestBody body: String): ResponseEntity<Unit> {
        MessageHandler.processor.onNext(MessageBuilder.withPayload(body).build())
        return ResponseEntity.ok().build()
    }
}
```

Sending messages using functional approach a bit tricky. 
You must use either [*StreamBridge*](https://cloud.spring.io/spring-cloud-static/spring-cloud-stream/3.0.4.RELEASE/reference/html/spring-cloud-stream.html#_using_streambridge) or [*EmitterProcessor*](https://cloud.spring.io/spring-cloud-static/spring-cloud-stream/3.0.4.RELEASE/reference/html/spring-cloud-stream.html#_using_reactor_api).  
We used the later approach here.

Fourthly and lastly, we put a lot of properties in the *application.properties* file.
The properties split into two groups. The first group contains RabbitMQ specific parameters. The second group contains Spring Cloud Stream specific parameters.

```
# rabbit properties
spring.rabbitmq.host=localhost
spring.rabbitmq.port=5672
spring.rabbitmq.username=user
spring.rabbitmq.password=password

# Spring cloud stream properties
# Both producer and consumer could create an exchange

spring.cloud.stream.function.definition: send;receive;

# producer
spring.cloud.stream.bindings.send-out-0.destination=myExchange
spring.cloud.stream.bindings.send-out-0.content-type=application/json
spring.cloud.stream.rabbit.bindings.send-out-0.producer.routing-key-expression='example'

# consumer
# Only consumers create a queue
spring.cloud.stream.bindings.receive-in-0.destination=myExchange
spring.cloud.stream.bindings.receive-in-0.content-type=application/json
spring.cloud.stream.bindings.receive-in-0.group=input
spring.cloud.stream.rabbit.bindings.receive-in-0.consumer.binding-routing-key=example
```

Spring Cloud Stream uses the following rules to describe properties for a producer:

```
spring.cloud.stream.bindings.<functionname-out-index>.destination=<exchange_name>
spring.cloud.stream.bindings.<functionname-out-index>.content-type=application/json
spring.cloud.stream.rabbit.bindings.<functionname-out-index>.producer.routing-key-expression='<queue_name>'
```

and for a consumer:

```
spring.cloud.stream.bindings.<functionname-in-index>.destination=<exchange_name>
spring.cloud.stream.bindings.<functionname-in-index>.content-type=application/json
spring.cloud.stream.bindings.<functionname-in-index>.group=<queue_name>
spring.cloud.stream.rabbit.bindings.<functionname-in-index>.consumer.binding-routing-key=<queue_name>
```

You could find more information about functional binding names [here](https://cloud.spring.io/spring-cloud-static/spring-cloud-stream/3.0.4.RELEASE/reference/html/spring-cloud-stream.html#_functional_binding_names).

Spring Cloud Stream will automatically create an exchange for each *destination* and a queue for each *group* at the application startup.
It will also bind the queue to the exchange based on the correspondent *binding-routing-key*.

I want to highlight that both a producer and consumer could create an exchange, but only a consumer can create a queue.
You also may notice that producer *routing-key-expression* is put into quotes.

You could easily run a [rabbitmq with help of docker](https://hub.docker.com/_/rabbitmq/) to run the whole application.

### Conclusion

We have reviewed Spring Cloud Stream *functional* approach.
You could check out the source code [here](https://github.com/vmaks/spring-cloud-stream-example/tree/master/function-rabbit).
You could find more examples [here](https://github.com/spring-cloud/spring-cloud-stream-samples) as well.

