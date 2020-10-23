---
layout: post
title:  "How to use Spring Cloud Stream functional annotation-based approach"
date:   2020-06-03 22:32:00 +0300
categories: other
---

### Overview

We are going to describe how to create an application with [Spring Cloud Stream](https://cloud.spring.io/spring-cloud-static/spring-cloud-stream/3.0.4.RELEASE/reference/html/index.html).
I have to mention that Spring Cloud Stream has a new way to describe message endpoints since 3.0.
The new way or so-called [*function*](https://cloud.spring.io/spring-cloud-static/spring-cloud-stream/3.0.4.RELEASE/reference/html/spring-cloud-stream.html#spring_cloud_function) approach to describe message endpoints. 
In the post we are going to review the old way or so-called [*annotation-based*](https://cloud.spring.io/spring-cloud-static/spring-cloud-stream/3.0.4.RELEASE/reference/html/spring-cloud-stream.html#_annotation_based_support_legacy) approach.

### Spring Cloud Stream Hello World

We are going to build a Spring application with Gradle build system, Kotlin, Spring Cloud Stream 3.0.4.RELEASE and use [topic exchange to route messages to the correspondent queue based on the routing key](https://www.rabbitmq.com/tutorials/tutorial-five-python.html) in the example.

Firstly, [create an application](https://start.spring.io/) with the following dependencies: Spring Web, Cloud Stream, Spring for RabbitMQ. 

```
package com.maks.springcloudstream

import org.springframework.boot.autoconfigure.SpringBootApplication
import org.springframework.boot.runApplication
import org.springframework.cloud.stream.annotation.EnableBinding

@EnableBinding(Queues::class)
@SpringBootApplication
class SpringCloudStreamApplication

fun main(args: Array<String>) {
	runApplication<SpringCloudStreamApplication>(*args)
}
```

Secondly, create an interface with a method with *@Input* or *@Output* annotation. 
*@Input/@Output* annotation means corresponds to the input/output queue respectively.
You could find more information about naming [here](https://cloud.spring.io/spring-cloud-static/spring-cloud-stream/3.0.4.RELEASE/reference/html/spring-cloud-stream.html#_annotation_based_binding_names_legacy).
We used *@EnableBinding(Queues::class)* annotation in the previous code snippet to enable *@Input/@Output* annotations.

```
package com.maks.springcloudstream

import org.springframework.cloud.stream.annotation.Input
import org.springframework.cloud.stream.annotation.Output
import org.springframework.messaging.MessageChannel
import org.springframework.messaging.SubscribableChannel
import org.springframework.stereotype.Service

interface Queues {

    companion object {
        const val OUTPUT = "myOutput"
        const val INPUT = "myInput"
    }

    @Output(OUTPUT)
    fun myOutput(): MessageChannel

    @Input(INPUT)
    fun myInput(): SubscribableChannel
}
```

Thirdly, we create a Controller with a method that sends a message into the output queue.

```
package com.maks.springcloudstream

import org.apache.http.client.methods.RequestBuilder
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.cloud.stream.annotation.EnableBinding
import org.springframework.http.ResponseEntity
import org.springframework.messaging.handler.annotation.SendTo
import org.springframework.messaging.support.MessageBuilder
import org.springframework.web.bind.annotation.PostMapping
import org.springframework.web.bind.annotation.RequestBody
import org.springframework.web.bind.annotation.RequestMapping
import org.springframework.web.bind.annotation.RestController

@RestController
@RequestMapping("/")
class MessageController(private val queues: Queues) {

    @PostMapping
    fun sendMessage(@RequestBody body: String): ResponseEntity<Unit> {
        queues.myOutput().send(MessageBuilder.withPayload(body).build())
        return ResponseEntity.ok().build()
    }
}
```

Fourthly, we create a class with a method that is annotated with *@StreamListener(Queues.INPUT)* annotation.

```
package com.maks.springcloudstream

import org.slf4j.Logger
import org.slf4j.LoggerFactory
import org.springframework.cloud.stream.annotation.StreamListener
import org.springframework.stereotype.Service

@Service
class MessageHandler {

    val logger: Logger = LoggerFactory.getLogger(MessageHandler::class.java)

    @StreamListener(Queues.INPUT)
    fun receive(message: String) {
        logger.info(message)
    }
}
```

Fifthly and lastly, we put a lot of properties in the *application.properties* file.
The properties split into two groups. The first group contains RabbitMQ specific parameters. The second group contains Spring Cloud Stream specific parameters.

```
# rabbit properties
spring.rabbitmq.host=localhost
spring.rabbitmq.port=5672
spring.rabbitmq.username=user
spring.rabbitmq.password=password

# Spring Cloud Stream properties
# Both producer and consumer could create an exchange

# producer
spring.cloud.stream.bindings.myOutput.destination=myExchange
spring.cloud.stream.bindings.myOutput.content-type=application/json
spring.cloud.stream.rabbit.bindings.myOutput.producer.routing-key-expression='example'

# consumer
# Only consumers create a queue
spring.cloud.stream.bindings.myInput.destination=myExchange
spring.cloud.stream.bindings.myInput.content-type=application/json
spring.cloud.stream.bindings.myInput.group=input
spring.cloud.stream.rabbit.bindings.myInput.consumer.binding-routing-key=example
```

Spring Cloud Stream uses the following rules to describe properties for a producer:

```
spring.cloud.stream.bindings.<name_in_the_@Output_annotation>.destination=<exchange_name>
spring.cloud.stream.bindings.<name_in_the_@Output_annotation>.content-type=application/json
spring.cloud.stream.rabbit.bindings.<name_in_the_@Output_annotation>.producer.routing-key-expression='<queue_name>'
```

and for a consumer:

```
spring.cloud.stream.bindings.<name_in_the_@Input>.destination=<exchange_name>
spring.cloud.stream.bindings.<name_in_the_@Input>.content-type=application/json
spring.cloud.stream.bindings.<name_in_the_@Input>.group=<queue_name>
spring.cloud.stream.rabbit.bindings.<name_in_the_@Input_annotation>.consumer.binding-routing-key=<queue_name>
```

Spring Cloud Stream will automatically create an exchange for each *destination* and a queue for each *group* at the application startup.
It will also bind the queue to the exchange based on the correspondent *binding-routing-key*.

I want to highlight that both a producer and consumer could create an exchange, but only a consumer can create a queue.
You also may notice that producer *routing-key-expression* is put into quotes.

You could easily run a [rabbitmq with help of docker](https://hub.docker.com/_/rabbitmq/) to run the whole application.

### Conclusion

We have reviewed Spring Cloud Stream *annotation-based* approach.
You could check out the source code [here](https://github.com/vmaks/spring-cloud-stream-example/tree/master/annotation-rabbit).
You could find more examples [here](https://github.com/spring-cloud/spring-cloud-stream-samples) as well.

