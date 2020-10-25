---
layout: post
title:  "Hibernate many-to-many unidirectional relationship"
date:   2020-04-12 23:34:00 +0300
tags: [Spring, Kotlin, Spring Data, Hibernate]
---

### Overview

We could handle a many-to-many relationship in a database with the help of many-to-many bidirectional or unidirectional hibernate relationships.
In the post, we are going to discuss a many-to-many unidirectional hibernate relationship.

### Many-to-many unidirectional relationship

We are going to review an example with Person and Book which is both as many sides of a relationship.

An entity for the Person looks like this:

```
package com.example.demo

import javax.persistence.*

/**
 * unidirectional
 */
@Entity
@Table(name = "PERSON")
class Person(
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "PERSON_ID")
    val id: Long = 0,
    @Column(name = "NAME")
    val name: String
) {
    override fun equals(other: Any?): Boolean {
        if (this === other) return true
        if (javaClass != other?.javaClass) return false

        other as Person

        if (id == 0L && id != other.id) return false

        return true
    }

    override fun hashCode(): Int {
        return 13
    }
}
```

The is no difference from a simple Entity for Person.

An entity for the Book looks like this:

```
package com.example.demo

import javax.persistence.*

/**
 * unidirectional
 */
@Entity
@Table(name = "BOOK")
class Book(
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "BOOK_ID")
    val id: Long = 0,
    @Column(name = "NAME")
    val name: String,
    @ManyToMany(cascade = [CascadeType.ALL])
    @JoinTable(name = "mapping_person_book",
        joinColumns = [JoinColumn(name = "BOOK_ID")],
        inverseJoinColumns = [JoinColumn(name = "PERSON_ID")])
    var persons: List<Person>? = null
) {
    override fun equals(other: Any?): Boolean {
        if (this === other) return true
        if (javaClass != other?.javaClass) return false

        other as Book

        if (id == 0L && id != other.id) return false

        return true
    }

    override fun hashCode(): Int {
        return 13
    }
}
```

*@JoinTable* - is specifies the mapping of associations. It is applied to the owning side of an association.

*cascade = [CascadeType.ALL]* - it helps you propagate Person changes when working with Book.

The main difference from a simple Entity is the usage of *@JoinTable* and *@ManyToMany* annotations.

A repository for the Person looks like this:

```
package com.example.demo

import org.springframework.data.jpa.repository.JpaRepository
import org.springframework.stereotype.Repository

@Repository
interface PersonRepository : JpaRepository<Person, Long>
```

A repository for the Book looks like this:

```
package com.example.demo

import org.springframework.data.jpa.repository.JpaRepository
import org.springframework.stereotype.Repository

@Repository
interface BookRepository : JpaRepository<Book, Long>
```

There is an example of the usage of many-to-many unidirectional relationship below:

```
package com.example.demo

import org.springframework.boot.CommandLineRunner
import org.springframework.boot.autoconfigure.SpringBootApplication
import org.springframework.boot.runApplication
import org.springframework.transaction.annotation.Transactional

@SpringBootApplication
class DemoApplication(
	private val personRepository: PersonRepository,
	private val bookRepository: BookRepository
) : CommandLineRunner {
	@Transactional
	override fun run(vararg args: String?) {
		val person = Person(name = "Person1") // Create parent

		val book1 = Book(name = "Book1") // Create child
		book1.persons = listOf(person) // Set parent
		bookRepository.save(book1) // Save child

		val book2 = Book(name = "Book2") // Create child
		book2.persons = listOf(person) // Set parent
		bookRepository.save(book2) // Save child
	}
}

fun main(args: Array<String>) {
	runApplication<DemoApplication>(*args)
}
```

Firstly, create a person. 

Secondly, create a book1. 

Thirdly, set the person to the book1 field. 

Lastly, save the book1.

### Conclusion

We have discussed a many-to-many unidirectional relationship.
You could check out the source code [here](https://github.com/vmaks/kotlin-hibernate-equals-hashcode).
