---
layout: post
title:  "Hibernate many-to-many bidirectional relationship"
date:   2020-04-05 20:05:00 +0300
categories: other
---

### Overview

We could handle a many-to-many relationship in a database with the help of many-to-many bidirectional or unidirectional hibernate relationships.
If you want to learn how to implement a many-to-many unidirectional hibernate relationship you could look into the [following post](https://vmaks.github.io/other/2020/04/12/hibernate-many-to-many-unidirectional-relationship.html).
In the post, we are going to discuss a many-to-many bidirectional relationship. 

### Many-to-many bidirectional relationship

We are going to review an example with Person and Book which is both as many sides of a relationship.

An entity for the Person looks like this:

```
package com.example.demo

import javax.persistence.*

/**
 * bidirectional
 */
@Entity
@Table(name = "PERSON")
class Person(
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "PERSON_ID")
    val id: Long = 0,
    @Column(name = "NAME")
    val name: String,
    @ManyToMany(cascade = [CascadeType.ALL], mappedBy = "persons")
    var books: List<Book>? = null
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

*mappedBy* - indicates the inverse of the relationship.

*cascade = [CascadeType.ALL]* - it helps you propagate Book changes when working with Person.

The main difference from a simple Entity is the usage of *@ManyToMany* annotation.

An entity for the Book looks like this:

```
package com.example.demo

import javax.persistence.*

/**
 * bidirectional
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

As you can see you have access to the Book from the Person side compare to the [unidirectional relationship](https://vmaks.github.io/other/2020/04/12/hibernate-many-to-many-unidirectional-relationship.html).

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

There is an example of the usage of many-to-many bidirectional relationship below:

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

		val book2 = Book(name = "Book2") // Create child
		book2.persons = listOf(person) // Set parent

		person.books = listOf(book1, book2) // Set child's to parent
		personRepository.save(person) // Save parent
	}
}

fun main(args: Array<String>) {
	runApplication<DemoApplication>(*args)
}
```

Firstly, create a person. 

Secondly, create a book1. 

Thirdly, set the person to the book1 field. 

Fourthly, set books to the person.

Lastly, save the person.

### Conclusion

We have discussed a many-to-many bidirectional relationship.
You could check out the source code [here](https://github.com/vmaks/kotlin-hibernate-equals-hashcode).
