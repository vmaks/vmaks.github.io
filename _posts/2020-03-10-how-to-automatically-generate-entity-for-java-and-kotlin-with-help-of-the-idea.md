---
layout: post
title:  "How to automatically generate entity for Java and Kotlin with help of the IDEA"
date:   2020-03-10 23:00:00 +0300
categories: other
---

### Overview

You could easily generate [Java](https://stackoverflow.com/a/5263748/3001953) and Kotlin entities for a database with help of the IDEA.
In the post, we are going to discuss how to generate Java and Kotlin entities for a database with the help of the IDEA.

### How to generate entities for Java

Firstly, open *View/Tool Windows/Persistence*.

Secondly, right-click on a *module/Generate Persistence Mapping/By Database Schema*

Thirdly, choose *Data Source*. 

Finally, select a table, set package name and click OK.

### How to generate entities for Kotlin

Firstly, select any table in the Database view.

Then call *Scripted Extensions->Generate Kotlin Entitiese.kts*.

Notice: do not forget to remove *open* specifier before a class name in case of usage *kotlin-maven-allopen* plugin otherwise you will get a compiler error.

If you will get an error then simply restart the IDE. 

### Conclusion

We have described how to generate Java and Kotlin entities for a database with the help of the IDEA.
