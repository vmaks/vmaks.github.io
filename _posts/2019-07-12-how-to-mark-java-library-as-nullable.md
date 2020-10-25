---
layout: post
title:  "How to mark Java library as nullable"
date:   2019-07-12 23:00:00 +0300
tags: [Kotlin, Java]
---

Sometimes it is useful to mark a Java library as Nullable so you will be able to use it safely from Kotlin.

Let's start with a simple example. The example consists of a kotlin project that depend on a java library.

The following picture shows a simple code example.

![]({{ site.url }}/assets/2019-07-12-java-nullable-1.jpg)

Our goal is to change Java types from [Platform](https://kotlinlang.org/docs/reference/java-interop.html) to Nullable.

Add [jsr305](https://mvnrepository.com/artifact/com.google.code.findbugs/jsr305) dependency to a java library pom file:
```
<dependency>
    <groupId>com.google.code.findbugs</groupId>
    <artifactId>jsr305</artifactId>
    <version>3.0.2</version>
</dependency>
```

Create a [NullableApi annotation](https://dzone.com/articles/null-safety-calling-java-from-kotlin) like the following below:
```
package com.maks.foo;

import javax.annotation.Nullable;
import javax.annotation.meta.TypeQualifierDefault;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@TypeQualifierDefault({ElementType.METHOD, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.PACKAGE)
@Nullable
public @interface NullableApi {
}
```

Add a *package-info.java* file to the package (right click on a module in IDE) and mark the package with it. Look at the picture below

```
@NullableApi
package com.maks.foo;
```

In the end you should be able to see Nullable Java types in Kotlin code. It means that everything works.

![]({{ site.url }}/assets/2019-07-12-java-nullable-2.jpg)

If you don't know how to enable Kotlin type hints then you could look at [the following post](https://vmaks.github.io/other/2019/06/23/kotlin-type-hints-in-IDEA.html).

You could get a code of the example [here](https://github.com/vmaks/java-library-nullable)
