---
layout: post
title:  "How to show SQL statements and parameters with Hibernate"
date:   2020-07-26 17:31:00 +0300
tags: [Spring, Hibernate]
---

### Overview

Sometimes you may want to debug your SQL queries that are generated by [Hibernate](https://hibernate.org/).
In the post, we are going to discuss how to show SQL statements and parameters with Hibernate.

### Enable ability to show SQL statements and parameters

Simple add [the following code](https://www.baeldung.com/sql-logging-spring-boot) to the *application.properties* file:

```
# Show sql statement
logging.level.org.hibernate.SQL=debug

# Show sql parameters
logging.level.org.hibernate.type.descriptor.sql=trace
```

### Conclusion

We have discussed how to show SQL statements and parameters with Hibernate.
