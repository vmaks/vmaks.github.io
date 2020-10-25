---
layout: post
title:  "Kotlin type hints in IDEA"
date:   2019-06-23 18:28:00 +0300
tags: [Kotlin, IDE]
---

Kotlin type inference is a cool thing. It literally allows you to type less. 

However, in some cases it can be confusing which type is returned by a method that is called.

To overcome this issue we could use type hints with help of IDEA. 

I checked this feature with IDEA 2019.1. 

To enable the feature use the following steps below.

Firstly, look at the simple example below

![]({{ site.url }}/assets/2019-06-23-kotlin type_hints_in_idea-1.jpg)

Secondly, you have to open parameters in *Settings/Editor/General/Appearance/Configure*, then change language to Kotlin, then check all options. Don't forget to check *Show parameters name hints*. 

Look at the image below for additional information.

![]({{ site.url }}/assets/2019-06-23-kotlin type_hints_in_idea-2.jpg)

In the end, you will get the result like this below.

![]({{ site.url }}/assets/2019-06-23-kotlin type_hints_in_idea-3.jpg)
