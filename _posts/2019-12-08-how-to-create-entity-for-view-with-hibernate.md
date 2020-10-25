---
layout: post
title:  "How to create an entity for a view with the help of the Hibernate"
date:   2019-12-08 20:33:00 +0300
tags: [Kotlin, Hibernate]
---

### Overview

Using a view in a database is not popular nowadays as it was before especially with [microservice architecture](https://microservices.io/).

However, you could face it in a project.

A view is [a virtual table based on the result-set of an SQL statement](https://www.w3schools.com/sql/sql_view.asp).

In the post, we are going to implement an entity for a view with the help of the [Hibernate](https://hibernate.org/).

### Entity for a view with the help of the Hibernate

Suppose, we have a view in a database with the "EXAMPLE" name.

Then, an entity for the view may look like this (I omit all properties except ID, *equals()/hashCode()* for brevity):

```
@Entity
@Immutable
@Subselect("SELECT * FROM EXAMPLE")
class MyEntity(
	@Id
	@Column(name = "ID"),
	val id: Long = 0,

	...

	) {
	// equals()

	// hashCode()
}
```

@Entity - [it declares the class as an entity (i.e. a persistent POJO class)](https://docs.jboss.org/hibernate/annotations/3.5/reference/en/html/entity.html#entity-hibspec-entity).

@Immutable - it means read-only.

@Subselect - it means to map an immutable and read-only entity to a given SQL select expression.

### Conclusion

We have described how to implement an entity for a view with the help of the Hibernate.

Links:

http://lifeinide.com/post/2017-08-22-views-in-hibernate/
