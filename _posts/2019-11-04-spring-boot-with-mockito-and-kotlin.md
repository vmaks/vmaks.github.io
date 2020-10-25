---
layout: post
title:  "Spring boot with Mockito and Kotlin"
date:   2019-11-04 21:19:00 +0300
tags: [Kotlin, Spring, Mockito]
---

### Overview

It's a good practice to write a unit test to verify your code because it's a basement of the [Testing Pyramid](https://martinfowler.com/articles/practical-test-pyramid.html).
A unit test usually checks a single method.
In most cases, it is hard to write a unit test without mocking external dependencies such as database, external web server, etc. 

In this post, we will explore how to how to write a mock unit test with Spring boot and Kotlin.

### Mockito library

There is a library that helps you to create a mock. 
The library is [Mockito](https://github.com/mockito/mockito). 
I recommend to use a [thin Kotlin wrapper](https://github.com/nhaarman/mockito-kotlin)) around the library.

Firstly, you need to add dependencies to a pom file:

```
<!-- https://mvnrepository.com/artifact/com.nhaarman/mockito-kotlin -->
<dependency>
    <groupId>com.nhaarman</groupId>
    <artifactId>mockito-kotlin</artifactId>
    <version>1.6.0</version>
    <scope>test</scope>
</dependency>

```

### Inject a mock with the help of @Mock and @InjectMocks

Then, we will be able to mock a bean and inject it with the help of *@Mock* and *@InjectMocks* annotations correspondingly:

```
@RunWith(MockitoJUnitRunner::class)
class ExampleMockTest {
	@Mock
	lateinit var dependancy: Dependency

	@InjectMocks
	lateinit var service: Service

	@Test
	fun foo() {
		...
	}	
}
```

*@RunWith(MockitoJUnitRunner::class)* - it is used to tell the [JUnit](https://junit.org/junit4/) an entry point

### Inject a mock directly through a constructor

You could also inject a mock directly through a constructor of a bean. 
In some cases it could be helpful. 
For example, if a constructor parameter as String type. 

Let's see an example:

```
@RunWith(MockitoJUnitRunner::class)
class ExampleMockTest {
	@Mock
	lateinit var dependancy: Dependency

	lateinit var service: Service

	@Test
	fun foo() {
		service = Service(dependancy)
	}	
}
```

### Inject a mock bean into an Application context

Sometimes we need to inject a mock bean into an [Application context](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationContext.html).
We have to use *@MockBean* and *@Autowired* instead of *@Mock* and *@InjectMocks* annotations correspondingly.
Also, don't forget to put all required Spring annotations on a test class instead of *@RunWith(MockitoJUnitRunner::class)*.

There is an example below:

```
@ActiveProfiles("test")
@RunWith(SpringRunner::class)
@SpringBootTest
@TestPropertySource(locations = ["classpath:application-test.properties"])
class ExampleSpringMockTest {
	@MockBean
	lateinit var dependancy: Dependency

	@Autowired
	lateinit var service: Service

	@Test
	fun foo() {
		...
	}
}
```

*@RunWith(SpringRunner::class)* - it is used to tell the JUnit an entry point

*@ActiveProfiles("test")* - enable *test* profile if needed

*@TestPropertySource(locations = ["classpath:application-test.properties"])* - read properties from *application-test.properties* file

### Conclusion

We have described three scenarios of usage the Mockito with Spring boot and Kotlin. 
In particular, we described how to inject a mock with the help of *@Mock* and *@InjectMocks*, directly through a constructor of a bean and into an Application context.
