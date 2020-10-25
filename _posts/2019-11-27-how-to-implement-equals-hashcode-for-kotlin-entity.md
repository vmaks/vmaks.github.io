---
layout: post
title:  "How to implement equals() and hashCode() for a Kotlin entity"
date:   2019-11-27 22:11:00 +0300
tags: [Kotlin, Hibernate, Maven]
---

### Overview

In [the previous post](https://vmaks.github.io/other/2019/11/18/how-to-implement-equals-hashcode-for-java-entity.html) we have reviewed three ways of how to implement *equals()/hashCode()* for a Java entity.

In the post we are going to describe how to implement *equals()/hashCode()* for a Kotlin entity, discuss the usage Kotlin jpa compiler plugin and Kotlin data class for an entity.

### Different types of implementation *equals()/hashCode()*

Let's recall three main types of how to implement *equals()/hashCode()* for an entity: using a Generated Primary Key, using a Programmatically Managed Primary Key and using a Business Key.

Also, implementation of *equals()/hashCode()* has to follow equals and hashCode [contract](https://vmaks.github.io/other/2019/11/18/how-to-implement-equals-hashcode-for-java-entity.html).

### Generated Primary Key approach

Generated Primary Key approach calculates *equals()/hashCode()* based on sequence generated primary key:

```
import java.time.LocalDate
import javax.persistence.Entity
import javax.persistence.GeneratedValue
import javax.persistence.GenerationType
import javax.persistence.Id

/**
 * Using a Generated Primary Key
 */
@Entity
class MyEntity(
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    val id: Long = 0,
    val date: LocalDate,
    val message: String
) {
    override fun equals(other: Any?): Boolean {
        if (this === other) return true
        if (javaClass != other?.javaClass) return false

        other as MyEntity

        if (id == 0L && id != other.id) return false

        return true
    }

    override fun hashCode(): Int {
        return 13
    }
}
```

Notice that in *hashCode()* implementation we return the same number.
Otherwise, we will break the contract due to different id values before and after saving to a database.

The main difference between Kotlin and Java version is the fact that Kotlin version uses immutable and nonnull fields.
Hibernate assumes that an uninitialized entity has id field set to either 0 or null.
As a result, we need to set id field value to 0 for an uninitialized entity and check for 0 instead of null in the equals method compare to the Java version.

### Programmatically Managed Primary Key approach

Programmatically Managed Primary Key approach calculates *equals/hashCode* based on a constructor parameter:

```
import java.time.LocalDate
import javax.persistence.Entity
import javax.persistence.Id

/**
 * Using a Programmatically Managed Primary Key
 */
@Entity
class MyEntity(
    @Id
    val id: Long,
    val date: LocalDate,
    val message: String
) {
    override fun equals(other: Any?): Boolean {
        if (this === other) return true
        if (javaClass != other?.javaClass) return false

        other as MyEntity

        if (id == 0L && id != other.id) return false

        return true
    }

    override fun hashCode(): Int {
        return id.hashCode()
    }
}
```

In this approach, a unique id is generated outside and we use a standard implementation of *equals()/hashCode()* for an id parameter except for the fact that we check for 0 instead of null in the equals method compare to the Java version.

### Business Key approach

Business Key approach calculates *equals()/hashCode()* based on a constructor parameter:

```
import java.time.LocalDate
import javax.persistence.Entity
import javax.persistence.GeneratedValue
import javax.persistence.GenerationType
import javax.persistence.Id

/**
 * Using a Business Key
 */
@Entity
class MyEntity(
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    val id: Long = 0,
    val date: LocalDate,
    val message: String,
    val businessKey: String
) {
    override fun equals(other: Any?): Boolean {
        if (this === other) return true
        if (javaClass != other?.javaClass) return false

        other as MyEntity

        if (id == 0L && businessKey != other.businessKey) return false

        return true
    }

    override fun hashCode(): Int {
        return businessKey.hashCode()
    }
}
```

This approach is similar to the Programmatically Managed Primary Key approach except for the fact that we use a standard implementation of *equals()/hashCode()* for a businessKey parameter.

### Kotlin jpa compiler plugin

Both Hibernate and JPA require that [an entity class should have a no-argument constructor](https://docs.jboss.org/hibernate/orm/5.4/userguide/html_single/Hibernate_User_Guide.html#entity-pojo-constructor).

Kotlin does not generate default constructor by default.

However, we could use *jpa* compiler plugin (optionally add *spring* if you use spring) for that purpose:

```
<plugin>
  <groupId>org.jetbrains.kotlin</groupId>
  <artifactId>kotlin-maven-plugin</artifactId>
  <configuration>
    <args>
      <arg>-Xjsr305=strict</arg>
    </args>
    <compilerPlugins>
      <plugin>spring</plugin>
      <plugin>jpa</plugin>
    </compilerPlugins>
  </configuration>
  <dependencies>
    <dependency>
      <groupId>org.jetbrains.kotlin</groupId>
      <artifactId>kotlin-maven-allopen</artifactId>
      <version>${kotlin.version}</version>
    </dependency>
    <dependency>
      <groupId>org.jetbrains.kotlin</groupId>
      <artifactId>kotlin-maven-noarg</artifactId>
      <version>${kotlin.version}</version>
    </dependency>
  </dependencies>
</plugin>
```

*jpa* plugin [generates no-arg constructor](https://kotlinlang.org/docs/reference/compiler-plugins.html#jpa-support) for a @Entity, @Embeddable and @MappedSuperclass annotations automatically.

### Kotlin data class

Personally, I don't recommend to use a data class for an entity. 
The reason for that is because data class automatically generates wrong implementation for *equals()/hashCode()* that do not follow the [contract](https://vmaks.github.io/other/2019/11/18/how-to-implement-equals-hashcode-for-java-entity.html).

### Conclusion

We have described three ways of how to implement *equals()/hashCode()* for a Kotlin entity. 
In particular, we described how to implement *equals()/hashCode()* using a Generated Primary Key, using a Business Key and using a Programmatically Managed Primary Key.
We also compared Kotlin and Java versions for the same entity.
The main difference between them is the fact that Kotlin version uses immutable and nonnull fields.
We also discussed the usage Kotlin jpa compiler plugin and Kotlin data class for an entity.
