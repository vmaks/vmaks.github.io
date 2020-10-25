---
layout: post
title:  "How to set up multiple databases with Spring Boot"
date:   2020-02-02 22:38:00 +0300
tags: [Spring, Spring Data]
---

### Overview

Sometimes you may need to set up multiple databases in a [Spring Boot](https://spring.io/projects/spring-boot) application.
For example, you may read data from the first database and write to the second one.
In the post, we are going to discuss how to set up multiple databases in a spring boot application.

### Multiple databases in a Spring Boot application

Firstly, we need to define properties for both databases in the *application.properties* file: 

```
spring.datasource.jdbcUrl = [url]
spring.datasource.username = [username]
spring.datasource.password = [password]

spring.second-datasource.jdbcUrl = [url]
spring.second-datasource.username = [username]
spring.second-datasource.password = [password]
```

Secondly, we have to define *DataSource*, *LocalContainerEntityManagerFactoryBean* and *PlatformTransactionManager* for the first and second databases.

We use Kotlin an Spring Boot 2.2.4 in the example below.

You may provide the following configuration for the first database:

```
import org.springframework.beans.factory.annotation.Qualifier
import org.springframework.boot.context.properties.ConfigurationProperties
import org.springframework.boot.jdbc.DataSourceBuilder
import org.springframework.boot.orm.jpa.EntityManagerFactoryBuilder
import org.springframework.context.annotation.Bean
import org.springframework.context.annotation.Configuration
import org.springframework.context.annotation.Primary
import org.springframework.data.jpa.repository.config.EnableJpaRepositories
import org.springframework.orm.jpa.JpaTransactionManager
import org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean
import org.springframework.transaction.PlatformTransactionManager
import org.springframework.transaction.annotation.EnableTransactionManagement
import javax.sql.DataSource

@Configuration
@EnableJpaRepositories(
    basePackages = ["PACKAGE_NAME_OF_FIRST_REPOSITORIES"],
    entityManagerFactoryRef = "firstEntityManagerFactory",
    transactionManagerRef = "firstTransactionManager"
)
@EnableTransactionManagement
class FirstDBConfiguration {

    @Primary
    @Bean(name = ["firstDataSource"])
    @ConfigurationProperties(prefix = "spring.datasource")
    fun firstDataSource(): DataSource {
        return DataSourceBuilder.create().build()
    }

    @Primary
    @Bean(name = ["firstEntityManagerFactory"])
    fun firstEntityManager(builder: EntityManagerFactoryBuilder): LocalContainerEntityManagerFactoryBean {
        return builder.dataSource(firstDataSource())
            .packages("PACKAGE_NAME_OF_FIRST_ENTITIES")
            .persistenceUnit("first")
            .properties(SET_HIBERNATE_PROPERTIES_FOR_THE_FIRST_DATABASE_IF_NEEDED)
            .build()
    }

    @Primary
    @Bean(name = ["firstTransactionManager"])
    fun firstTransactionManager(
        @Qualifier("firstEntityManagerFactory")
        entityManagerFactory: LocalContainerEntityManagerFactoryBean
    ): PlatformTransactionManager {
        val transactionManager = JpaTransactionManager()
        transactionManager.entityManagerFactory = entityManagerFactory.getObject()
        return transactionManager
    }
}
```

You may provide the following configuration for the second database:

```
import org.springframework.beans.factory.annotation.Qualifier
import org.springframework.boot.context.properties.ConfigurationProperties
import org.springframework.boot.jdbc.DataSourceBuilder
import org.springframework.boot.orm.jpa.EntityManagerFactoryBuilder
import org.springframework.context.annotation.Bean
import org.springframework.context.annotation.Configuration
import org.springframework.data.jpa.repository.config.EnableJpaRepositories
import org.springframework.orm.jpa.JpaTransactionManager
import org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean
import org.springframework.transaction.PlatformTransactionManager
import org.springframework.transaction.annotation.EnableTransactionManagement
import javax.sql.DataSource

@Configuration
@EnableJpaRepositories(
    basePackages = ["PACKAGE_NAME_OF_SECOND_REPOSITORIES"],
    entityManagerFactoryRef = "secondEntityManagerFactory",
    transactionManagerRef = "secondTransactionManager"
)
@EnableTransactionManagement
class SecondDBConfiguration {

    @Bean(name = ["secondDataSource"])
    @ConfigurationProperties(prefix = "spring.second-datasource")
    fun secondDataSource(): DataSource {
        return DataSourceBuilder.create().build()
    }

    @Bean(name = ["secondEntityManagerFactory"])
    fun secondEntityManager(builder: EntityManagerFactoryBuilder): LocalContainerEntityManagerFactoryBean {
        return builder.dataSource(firstDataSource())
            .packages("PACKAGE_NAME_OF_SECOND_ENTITIES")
            .persistenceUnit("second")
            .properties(SET_HIBERNATE_PROPERTIES_FOR_THE_SECOND_DATABASE_IF_NEEDED)
            .build()
    }

    @Bean(name = ["secondTransactionManager"])
    fun secondTransactionManager(
        @Qualifier("secondEntityManagerFactory")
        entityManagerFactory: LocalContainerEntityManagerFactoryBean
    ): PlatformTransactionManager {
        val transactionManager = JpaTransactionManager()
        transactionManager.entityManagerFactory = entityManagerFactory.getObject()
        return transactionManager
    }
}
```

You may notice that we use *@Primary* annotation for the first database configuration beans so you may use it without the *@Qualifier*.

We use *DataSourceBuilder* and *EntityManagerFactoryBuilder* to create the *DataSource* and *LocalContainerEntityManagerFactoryBean* because it is the easiest way to configure them.

*PACKAGE_NAME_OF_FIRST_REPOSITORIES* and *PACKAGE_NAME_OF_SECOND_REPOSITORIES* are the names of the packages with repositories for the first and second database correspondingly.

*PACKAGE_NAME_OF_FIRST_ENTITIES* and *PACKAGE_NAME_OF_SECOND_ENTITIES* are the names of the packages with entities for the first and second database correspondingly.

*SET_HIBERNATE_PROPERTIES_FOR_THE_FIRST_DATABASE_IF_NEEDED* and *SET_HIBERNATE_PROPERTIES_FOR_THE_SECOND_DATABASE_IF_NEEDED* are used to provide additional options for a database such as [dialect and etc](https://www.tutorialspoint.com/hibernate/hibernate_configuration.htm) in the Map format.

For example, for Postgres  you may need to set the hibernate-dialect:

```
hibernate-dialect=org.hibernate.dialect.PostgreSQL95Dialect
```

You could read more information about how to set up multiple databases in a spring boot application by the following links:

https://www.baeldung.com/spring-data-jpa-multiple-databases

https://medium.com/@joeclever/using-multiple-datasources-with-spring-boot-and-spring-data-6430b00c02e7

### Conclusion

We have described how to set up multiple databases in a spring boot application.
