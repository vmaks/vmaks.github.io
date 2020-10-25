---
layout: post
title:  "Hibernate many-to-one unidirectional relationship"
date:   2020-03-23 23:00:00 +0300
tags: [Spring, Kotlin, Spring Data, Hibernate]
---

### Overview

We could handle a many-to-one relationship in a database with the help of many-to-one bidirectional or unidirectional hibernate relationships.
In the post, we are going to discuss a many-to-one unidirectional relationship.

### Many-to-one unidirectional relationship

We are going to review an example with Employer as one side and Employee as many side of a relationship.

An entity for the Employer looks like this:

```
package com.example.demo

import javax.persistence.*

/**
 * unidirectional
 */
@Entity
@Table(name = "EMPLOYER")
class Employer(
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "EMPLOYER_ID")
    val id: Long = 0,
    @Column(name = "NAME")
    val name: String
) {
    override fun equals(other: Any?): Boolean {
        if (this === other) return true
        if (javaClass != other?.javaClass) return false

        other as Employer

        if (id == 0L && id != other.id) return false

        return true
    }

    override fun hashCode(): Int {
        return 13
    }
}
```

An entity for the Employee looks like this:

```
package com.example.demo

import javax.persistence.*

/**
 * unidirectional
 */
@Entity
@Table(name = "EMPLOYEE")
class Employee(
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "EMPLOYEE_ID")
    val id: Long = 0,
    @Column(name = "NAME")
    val name: String,
    @JoinColumn(name = "EMPLOYER_ID")
    @ManyToOne(fetch = FetchType.LAZY, cascade = [CascadeType.ALL])
    var employer: Employer? = null
) {
    override fun equals(other: Any?): Boolean {
        if (this === other) return true
        if (javaClass != other?.javaClass) return false

        other as Employee

        if (id == 0L && id != other.id) return false

        return true
    }

    override fun hashCode(): Int {
        return 13
    }
}
```

*@JoinColumn* - it indicates the owner of the relationships, the corresponding table has a column with a foreign key to the reference table.

*FetchType.LAZY* - it helps you not to load data if it isn't requested.

*cascade = [CascadeType.ALL]* - it helps you propagate Employer changes when working with Employees.

The main difference from a simple Entity is the usage of *@JoinColumn* and *@ManyToOne* annotations.

As you can see you have no access to the Employee from the Employer side. You have to use additional request to find all Employees.

A repository for the Employer looks like this:

```
package com.example.demo

import org.springframework.data.jpa.repository.JpaRepository
import org.springframework.stereotype.Repository

@Repository
interface EmployerRepository : JpaRepository<Employer, Long>
```

A repository for the Employee looks like this:

```
package com.example.demo

import org.springframework.data.jpa.repository.JpaRepository
import org.springframework.stereotype.Repository

@Repository
interface EmployeeRepository : JpaRepository<Employee, Long>
```

There is an example of the usage of many-to-one unidirectional relationship below:

```
package com.example.demo

import org.springframework.boot.CommandLineRunner
import org.springframework.boot.autoconfigure.SpringBootApplication
import org.springframework.boot.runApplication
import org.springframework.transaction.annotation.Transactional

@SpringBootApplication
class DemoApplication(
	private val employeeRepository: EmployeeRepository,
	private val employerRepository: EmployerRepository
) : CommandLineRunner {
	@Transactional
	override fun run(vararg args: String?) {
		val employer = Employer(name = "Employer")  // Create parent

		val employee1 = Employee(name = "Employee1") // Create child
		employee1.employer = employer // Set parent for a child
		employeeRepository.save(employee1) // Save child

		val employee2 = Employee(name = "Employee2") // Create child
		employee2.employer = employer // Set parent for a child
		employeeRepository.save(employee2) // Save child
	}
}

fun main(args: Array<String>) {
	runApplication<DemoApplication>(*args)
}
```

Firstly, create an employer. 

Secondly, create an employee1. 

Thirdly, set the employer to the employee1 field. 

Lastly, save employee1.

### Conclusion

We have discussed a many-to-one unidirectional relationship.
You could check out the source code [here](https://github.com/vmaks/kotlin-hibernate-equals-hashcode).
