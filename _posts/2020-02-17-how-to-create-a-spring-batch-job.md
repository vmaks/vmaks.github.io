---
layout: post
title:  "How to create a Spring Batch job"
date:   2020-02-17 00:27:00 +0300
tags: [Spring, Spring Batch]
---

### Overview

[Spring Batch](https://spring.io/projects/spring-batch) is a great technology that helps you create repeatable tasks.
In the post, we are going to discuss how to create a Spring Batch job and launch it.

### Example

Firstly, add the following dependency to the pom.xml file:

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-batch</artifactId>
</dependency>
```

Then define a *Job, Step, JpaPagingItemReader, ItemProcessor*, and *JpaItemWriter*:

```
import org.springframework.batch.core.Job
import org.springframework.batch.core.Step
import org.springframework.batch.core.configuration.annotation.*
import org.springframework.batch.item.ItemProcessor
import org.springframework.batch.item.database.JpaItemWriter
import org.springframework.batch.item.database.JpaPagingItemReader
import org.springframework.batch.item.database.builder.JpaItemWriterBuilder
import org.springframework.batch.item.database.builder.JpaPagingItemReaderBuilder
import org.springframework.context.annotation.Bean
import org.springframework.context.annotation.Configuration
import org.springframework.context.annotation.Scope
import org.springframework.context.annotation.ScopedProxyMode
import org.springframework.beans.factory.config.ConfigurableBeanFactory
import javax.persistence.*

@Configuration
@EnableBatchProcessing
class ExampleJob(
    val jobBuilderFactory: JobBuilderFactory,
    val stepBuilderFactory: StepBuilderFactory,
    val entityManagerFactory: EntityManagerFactory
) {

    companion object {
        const val JOB_NAME = "exampleJob"
        private const val STEP_NAME = "exampleStep"
        private const val CHUNK_SIZE = 2
        private const val BOOK_READER = "bookReader"
        private const val PAGE_SIZE = 2
    }

    @Bean
    @Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE, proxyMode = ScopedProxyMode.TARGET_CLASS)
    fun job(): Job {
        return jobBuilderFactory.get(JOB_NAME)
            .flow(step())
            .end()
            .build()
    }

    @Bean
    @Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE, proxyMode = ScopedProxyMode.TARGET_CLASS)
    fun step(): Step {
        return stepBuilderFactory.get(STEP_NAME)
            .chunk<BookEntity, NewBookEntity>(CHUNK_SIZE)
            .reader(reader())
            .processor(processor())
            .writer(writer())
            .build()
    }

    @Bean
    @Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE, proxyMode = ScopedProxyMode.TARGET_CLASS)
    fun reader(): JpaPagingItemReader<BookEntity> {
        return JpaPagingItemReaderBuilder<BookEntity>()
            .name(BOOK_READER)
            .entityManagerFactory(entityManagerFactory)
            .queryString("SELECT b from BookEntity b")
            .pageSize(PAGE_SIZE)
            .build()
    }

    @Bean
    @Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE, proxyMode = ScopedProxyMode.TARGET_CLASS)
    fun processor(): ItemProcessor<BookEntity, NewBookEntity> {
        return ItemProcessor{ book: BookEntity -> NewBookEntity(title = book.title, author = book.author) }
    }

    @Bean
    @Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE, proxyMode = ScopedProxyMode.TARGET_CLASS)
    fun writer(): JpaItemWriter<NewBookEntity> {
        return JpaItemWriterBuilder<NewBookEntity>()
            .entityManagerFactory(entityManagerFactory)
            .build()
    }
}
```

*@EnableBatchProcessing* - [it enables additional things like JobLauncher](https://docs.spring.io/spring-batch/docs/current/api/org/springframework/batch/core/configuration/annotation/EnableBatchProcessing.html).

*@Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE, proxyMode = ScopedProxyMode.TARGET_CLASS)* - it helps us recreate all ExampleJob @Bean on each call by using [scoped-proxy](https://docs.spring.io/spring/docs/5.3.0-SNAPSHOT/spring-framework-reference/core.html#beans-factory-scopes-other-injection).

*BookEntity* looks simple:

```
import javax.persistence.Entity
import javax.persistence.GeneratedValue
import javax.persistence.GenerationType
import javax.persistence.Id

@Entity
class BookEntity(
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    val id: Long = 0,
    val title: String,
    val author: String
) {
    override fun equals(other: Any?): Boolean {
        if (this === other) return true
        if (javaClass != other?.javaClass) return false

        other as BookEntity

        if (id == 0L && id != other.id) return false

        return true
    }

    override fun hashCode(): Int {
        return 13
    }
}
```

*NewBookEntity* implemented the same way as *BookEntity* for simplicity:

```
import javax.persistence.Entity
import javax.persistence.GeneratedValue
import javax.persistence.GenerationType
import javax.persistence.Id

@Entity
class NewBookEntity(
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    val id: Long = 0,
    val title: String,
    val author: String
) {
    override fun equals(other: Any?): Boolean {
        if (this === other) return true
        if (javaClass != other?.javaClass) return false

        other as NewBookEntity

        if (id == 0L && id != other.id) return false

        return true
    }

    override fun hashCode(): Int {
        return 13
    }
}
```

Lastly, we create a *RestController* to launch the job:

```
import org.springframework.batch.core.ExitStatus
import org.springframework.batch.core.Job
import org.springframework.batch.core.JobParametersBuilder
import org.springframework.batch.core.launch.JobLauncher
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.beans.factory.annotation.Qualifier
import org.springframework.http.ResponseEntity
import org.springframework.web.bind.annotation.PostMapping
import org.springframework.web.bind.annotation.RequestMapping
import org.springframework.web.bind.annotation.RestController
import java.time.OffsetDateTime

@RestController
@RequestMapping("/")
class BatchController(private val jobLauncher: JobLauncher) {

    @Qualifier(ExampleJob.JOB_NAME)
    @Autowired
    lateinit var job: Job

    @PostMapping
    fun runJob(): ResponseEntity<ExitStatus> {
        val jobParameters = JobParametersBuilder()
            .addString("date", OffsetDateTime.now().toString())
            .toJobParameters()
        val exitStatus = jobLauncher.run(
            job, jobParameters
        ).exitStatus
        return ResponseEntity.ok(exitStatus)
    }
}
```

### Conclusion

We have described how to create a Spring Batch job and launch it through a *RestController*.
