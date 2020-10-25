---
layout: post
title:  "How to call a procedure in a database with spring boot"
date:   2020-08-02 17:53:00 +0300
tags: [Spring, Kotlin]
---

### Overview

Sometimes you need to call a procedure in a database.
In the post, we are going to discuss how to call a procedure in a database with spring jdbc template.

### Call a procedure in a database with spring jdbc template

The easiest way to call a procedure in a database is to use a [spring jdbc template](https://docs.spring.io/spring/docs/current/spring-framework-reference/data-access.html#jdbc-JdbcTemplate).

Spring jdbc template is just a wrapper around [JDBC](https://docs.oracle.com/javase/tutorial/jdbc/basics/index.html).

Firstly you have to add the following dependency to the project:

```
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jdbc</artifactId>
    </dependency>
```

Then you could create a JdbcTemplate and just call a procedure like in the example below:

```
val jdbcTemplate = JdbcTemplate()
jdbcTemplate.execute("call procedure_example()")
```

### Conclusion

We have discussed how to call a procedure in a database with spring jdbc template.
