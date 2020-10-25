---
layout: post
title:  "Hibernate one-to-one bidirectional relationship"
date:   2020-04-27 21:36:00 +0300
tags: [Spring, Kotlin, Spring Data, Hibernate]
---

### Overview 

We could handle a one-to-one relationship in a database with the help of one-to-one bidirectional or unidirectional hibernate relationships.
In the post, we are going to discuss a one-to-one bidirectional hibernate relationship. 

### One-to-one bidirectional relationship

We are going to review an example with Employee and Address which is both as one side of a relationship.

An entity for the Employee looks like this:

```
package com.example.demo

import javax.persistence.*

/**
 * bidirectional
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
    @OneToOne(mappedBy = "employee", cascade = [CascadeType.ALL])
    var address: Address? = null
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

*mappedBy* - indicates the inverse of the relationship.

*cascade = [CascadeType.ALL]* - it helps you propagate Address changes when working with Employee.

The main difference from a simple Entity is the usage of *@OneToOne* annotation.

An entity for the Address looks like this:

```
package com.example.demo

import javax.persistence.*

/**
 * bidirectional
 */
@Entity
@Table(name = "ADDRESS")
class Address(
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "ADDRESS_ID")
    val id: Long = 0,
    @Column(name = "NAME")
    val name: String,
    @JoinColumn(name = "EMPLOYEE_ID")
    @OneToOne(fetch = FetchType.LAZY, cascade = [CascadeType.ALL])
    var employee: Employee? = null
) {
    override fun equals(other: Any?): Boolean {
        if (this === other) return true
        if (javaClass != other?.javaClass) return false

        other as Address

        if (id == 0L && id != other.id) return false

        return true
    }

    override fun hashCode(): Int {
        return 13
    }
}
```

*@JoinColumn* - it indicates the owner of the relationships, the corresponding table has a column with a foreign key to the reference table.

*cascade = [CascadeType.ALL]* - it helps you propagate Employee changes when working with Address.

The main difference from a simple Entity is the usage of *@JoinColumn* and *@OneToOne* annotations.

A repository for the Employee looks like this:

```
package com.example.demo

import org.springframework.data.jpa.repository.JpaRepository
import org.springframework.stereotype.Repository

@Repository
interface EmployeeRepository : JpaRepository<Employee, Long>
```

A repository for the Address looks like this:

```
package com.example.demo

import org.springframework.data.jpa.repository.JpaRepository
import org.springframework.stereotype.Repository

@Repository
interface AddressRepository : JpaRepository<Address, Long>
```

There is an example of the usage of one-to-one unidirectional relationship below:

```
package com.example.demo

import org.springframework.boot.CommandLineRunner
import org.springframework.boot.autoconfigure.SpringBootApplication
import org.springframework.boot.runApplication
import org.springframework.transaction.annotation.Transactional

@SpringBootApplication
class DemoApplication(
	private val employeeRepository: EmployeeRepository
) : CommandLineRunner {
  @Transactional
	override fun run(vararg args: String?) {
		val employee = Employee(name = "Employee1") // Create parent
		val address = Address(name = "Address", employee = employee)  // Create child, set parent
		employee.address = address // Set child to parent
		employeeRepository.save(employee) // Save parent
	}
}

fun main(args: Array<String>) {
	runApplication<DemoApplication>(*args)
}

```

Firstly, create an employee. 

Secondly, create an address. 

Thirdly, set the address to the address field of the employee. 

Lastly, save the employee.

### Conclusion

We have discussed a one-to-one bidirectional relationship.
You could check out the source code [here](https://github.com/vmaks/kotlin-hibernate-equals-hashcode).
