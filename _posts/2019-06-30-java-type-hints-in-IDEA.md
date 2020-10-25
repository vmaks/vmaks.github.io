---
layout: post
title:  "Java type hints in IDEA"
date:   2019-06-30 12:48:00 +0300
tags: [Java, IDE]
---

Java 10 introduced a new [var keyword](http://openjdk.java.net/jeps/286) that allows you to not declare type explicitly. 

I am not going to discuss the pros and cons of the feature. You could read about it more [here](https://openjdk.java.net/projects/amber/LVTIFAQ.html) if you want.

However, it is possible to show the real type instead of a var keyword. 

I checked this feature with IDEA 2019.1. To enable the feature use the following steps below.

Firstly, look at the simple example below

![]({{ site.url }}/assets/2019-06-30-java type_hints_in_idea-1.jpg)

You could see that the example covers both const and not const cases.

Secondly, you have to open parameters in Settings/Editor/General/Code Folding, then check *Replace 'var' with inferred type*. Look at the image below.

![]({{ site.url }}/assets/2019-06-30-java type_hints_in_idea-2.jpg)

Then reopen a file if needed. 

In the end, you will get the result like this below.

![]({{ site.url }}/assets/2019-06-30-java type_hints_in_idea-3.jpg)
