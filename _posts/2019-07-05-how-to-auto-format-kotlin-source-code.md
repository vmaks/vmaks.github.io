---
layout: post
title:  "How to auto format Kotlin source code"
date:   2019-07-05 23:57:00 +0300
categories: other
---

Modern programming languages like [Go](https://golang.org/pkg/go/format/) and [Rust](https://github.com/rust-lang/rustfmt) use an additional tool that automatically formats source code according to the official code style. 

It helps to reduce [bikeshedding](https://exceptionnotfound.net/bikeshedding-the-daily-software-anti-pattern/) significantly.

Kotlin does not have built in solution.

Nevertheless, there are two solutions to the problem.

*First solution* 

Kotlin has [a great IDE](https://www.jetbrains.com/idea/) which helps you a lot. 

The IDE has ability to [reformat code](https://www.jetbrains.com/help/idea/reformat-file-dialog.html) explicitly according to the [official code style](https://kotlinlang.org/docs/reference/coding-conventions.html). 

There is [a Gradle plugin](https://github.com/JetBrains/inspection-plugin) that is able to launch IDE inspections, for example during a build. It has ability to format code as well.

However, there is no plugin for Maven right now. It is sad(

*Second solution* 

There is [a ktlint tool](https://ktlint.github.io/) that addresses the same problem. It has plugins for both Gradle and Maven build tools. You can set up it to auto format source code before [the compilation stage](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html). 

The following example shows you how to implement it with help of a Maven ktlint plugin.

You need to add the following snippet to pom.xml (inside ```<build><plugins>...</plugins></build>```)

```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-antrun-plugin</artifactId>
    <version>1.7</version>
    <executions>
        <execution>
            <id>ktlint-format</id>
            <phase>validate</phase>
            <configuration>
            <target name="ktlint">
                <java taskname="ktlint" dir="${basedir}" fork="true" failonerror="true"
                    classname="com.pinterest.ktlint.Main" classpathref="maven.plugin.classpath">
                    <arg value="-F"/>
                    <arg value="src/**/*.kt"/>
                </java>
            </target>
            </configuration>
            <goals><goal>run</goal></goals>
        </execution>
    </executions>
    <dependencies>
        <dependency>
            <groupId>com.pinterest</groupId>
            <artifactId>ktlint</artifactId>
            <version>0.33.0</version>
        </dependency>
    </dependencies>
</plugin>
```

I recommend to put it into parent pom.xml file in case of a  multi module project.

I recommend to change max number of *Blank Lines* to 1 for Kotlin Code Style in IntelliJ IDEA settings.

That is it! 

It works perfectly! 

Thank you for  reading!
