---
layout: post
title:  "How to enable partial compilation in IDEA"
date:   2020-07-18 22:40:00 +0300
categories: other
---

### Overview

Sometimes you may want to run a main method in one particular class despite having some errors in a project. 
[IntelliJ IDEA](https://www.jetbrains.com/idea/) prevents you from it by default.
In the post, we are going to discuss how to enable partial compilation in IDEA.

### Enable partial compilation in IDEA

You need to [switch to Eclipse compiler](https://stackoverflow.com/questions/16784703/enable-partial-compilation-in-intellij-idea) to do that.
Simply open IDEA Settings | Build, Execution, Deployment | Compiler | Java compiler and choose *Eclipse* compiler.
*Eclipse* compiler has an option to *Proceed on errors* by default.

You could look at the screenshot below

![]({{ site.url }}/assets/2020-07-18-how-to-enable-partial-compilation-in-idea-1.jpg)

### Conclusion

We have discussed how to enable partial compilation in IDEA.
