---
layout: post
title:  "How to override Java-style getters and setters in Kotlin"
date:   2020-01-20 22:29:00 +0300
tags: [Kotlin]
---

### Overview

Sometimes you may need to implement a Java interface for a Kotlin class.
In the post, we are going to discuss how to override Java-style getters and setters for a Kotlin class.

### Example

Suppose we have the following Java interface:

```Java
public interface A {
  Long getId();

  boolean isNew();
}
```

[Because Kotlin properties do not override Java-style getters and setters](https://youtrack.jetbrains.com/issue/KT-6653) you have to [add a private modifier for property and override a method](https://stackoverflow.com/a/32971284/3001953) in a Kotlin class:

```Kotlin
class B(
  private val id: Long,
  private val new: Boolean
): A {
    override fun getId() = id

    override fun isNew() = new
}
```

### Conclusion

We have described how to override Java-style getters and setters for a Kotlin class.
