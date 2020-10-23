---
layout: post
title:  "Hibernate one-to-one unidirectional relationship"
date:   2020-05-04 18:02:00 +0300
categories: other
---

### Overview 

We could handle a one-to-one relationship in a database with the help of one-to-one bidirectional or unidirectional hibernate relationships.
In the post, we are going to discuss a one-to-one unidirectional hibernate relationship. 

### One-to-one unidirectional relationship

We are going to review an example with Employee and Address which is both as one side of a relationship.

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
    val name: String
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

There is no difference between an Employee and a simple Entity.

An entity for the Address looks like this:

```
package com.example.demo

import javax.persistence.*

/**
 * unidirectional
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
	private val employeeRepository: EmployeeRepository,
	private val addressRepository: AddressRepository
) : CommandLineRunner {
	@Transactional
	override fun run(vararg args: String?) {
		val employee = Employee(name = "Employee1") // Create parent
		val address = Address(name = "Address") // Create child
		address.employee = employee // Set parent
		addressRepository.save(address) // Save child
	}
}

fun main(args: Array<String>) {
	runApplication<DemoApplication>(*args)
}
```

Firstly, create an employee. 

Secondly, create an address. 

Thirdly, set the employee to the address field. 

Lastly, save the address.

### Conclusion

We have discussed a one-to-one unidirectional relationship.
You could check out the source code [here](https://github.com/vmaks/kotlin-hibernate-equals-hashcode).
