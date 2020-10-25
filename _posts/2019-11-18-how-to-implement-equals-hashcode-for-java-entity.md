---
layout: post
title:  "How to implement equals() and hashCode() for a Java entity"
date:   2019-11-18 22:25:00 +0300
tags: [Java, Hibernate, Maven]
---

### Overview

Almost every web server has a database to store data.

In Java backed application the most popular solution is to use [Hibernate](https://hibernate.org/).

Hibernate is used to map an object into an entry in a table.

The most confusing part of writing an entity is how to implement *equals()/hashCode()* and different types of relationships between tables.

We are going to describe how to implement *equals()/hashCode()* for an entity.

### *equals()* and *hashCode()* contract 

Implementation of *equals()/hashCode()* has to follow equals and hashCode [contract](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/Object.html).

The general [contract of hashCode is](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/Object.html):

* Whenever it is invoked on the same object more than once during an execution of a Java application, the hashCode method must consistently return the same integer, provided no information used in equals comparisons on the object is modified. This integer need not remain consistent from one execution of an application to another execution of the same application.

* If two objects are equal according to the equals(Object) method, then calling the hashCode method on each of the two objects must produce the same integer result.

* It is not required that if two objects are unequal according to the equals(java.lang.Object) method, then calling the hashCode method on each of the two objects must produce distinct integer results. However, the programmer should be aware that producing distinct integer results for unequal objects may improve the performance of hash tables.

The general [contract of equals is](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/Object.html):

* It is reflexive: for any non-null reference value x, x.equals(x) should return true.

* It is symmetric: for any non-null reference values x and y, x.equals(y) should return true if and only if y.equals(x) returns true.

* It is transitive: for any non-null reference values x, y, and z, if x.equals(y) returns true and y.equals(z) returns true, then x.equals(z) should return true.

* It is consistent: for any non-null reference values x and y, multiple invocations of x.equals(y) consistently return true or consistently return false, provided no information used in equals comparisons on the objects is modified.

* For any non-null reference value x, x.equals(null) should return false.

### Different types of implementation *equals()/hashCode()*

There are three main types of how to implement *equals()/hashCode()* for an entity: using a Generated Primary Key, using a Programmatically Managed Primary Key and using a Business Key.

### Generated Primary Key approach

Generated Primary Key approach calculates *equals()/hashCode()* based on sequence generated primary key:

```
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import java.time.LocalDate;

/**
 * Using a Generated Primary Key
 */
public class MyEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    private LocalDate date;

    private String message;

    // get/set for id

    // get/set for date

    // get/set for message

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        MyEntity myEntity = (MyEntity) o;

        return id != null ? id.equals(myEntity.id) : myEntity.id == null;
    }

    @Override
    public int hashCode() {
        return 13;
    }
}
```

Notice that in *hashCode()* implementation we return the same number. It could lead to a bad performance un some cases, but we have to do it this way to satisfy *equals()* and *hashCode()* contract.
Otherwise, we will break the contract due to different id values before and after saving to a database.

### Programmatically Managed Primary Key approach

Programmatically Managed Primary Key approach calculates *equals/hashCode* based on a constructor parameter:

```
import javax.persistence.Id;
import java.time.LocalDate;

/**
 * Using a Programmatically Managed Primary Key
 */
public class MyEntity {

    @Id
    private Long id;

    private LocalDate date;

    private String message;

    private MyEntity() {}

    public MyEntity(Long id) {
        this.id = id;
    }
    
    // get/set for id

    // get/set for date

    // get/set for message

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        MyEntity myEntity = (MyEntity) o;

        return id != null ? id.equals(myEntity.id) : myEntity.id == null;

    }

    @Override
    public int hashCode() {
        return id != null ? id.hashCode() : 0;
    }
}
```

In this approach unique id is generated outside and we use standard implementation of *equals()/hashCode()* for an id parameter.

### Business Key approach

Business Key approach calculates *equals()/hashCode()* based on a constructor parameter:

```
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import java.time.LocalDate;

/**
 * Using a Business Key
 */
public class MyEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    private LocalDate date;

    private String message;

    private String businessKey;

    private MyEntity() {}

    public MyEntity(String businessKey) {
        this.businessKey = businessKey;
    }

    // get/set for id

    // get/set for date

    // get/set for message

    public String getBusinessKey() {
        return businessKey;
    }

    public void setBusinessKey(String businessKey) {
        this.businessKey = businessKey;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        MyEntity myEntity = (MyEntity) o;

        return businessKey != null ? businessKey.equals(myEntity.businessKey) : myEntity.businessKey == null;

    }

    @Override
    public int hashCode() {
        return businessKey != null ? businessKey.hashCode() : 0;
    }
}
```

This approach is similar to the Programmatically Managed Primary Key approach except for the fact that we use a standard implementation of *equals()/hashCode()* for a businessKey parameter.

### Conclusion

We have described three ways of how to implement *equals()/hashCode()* for an entity. 
In particular, we described how to implement *equals()/hashCode()* using a Generated Primary Key, using a Business Key and using a Programmatically Managed Primary Key.
First approach calculates *equals()/hashCode()* based on sequence generated primary key.
The other two approaches calculate *equals()/hashCode()* based on a constructor parameter.
