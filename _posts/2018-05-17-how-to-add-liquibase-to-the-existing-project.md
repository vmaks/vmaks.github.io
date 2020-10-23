---
layout: post
title:  "How to add liquibase to the existing project"
date:   2018-05-17 22:05:00 +0300
categories: other
---

### Overview

Sometimes you need to track changes in a database.

Hopefully, there is exist an open-source and free solution to the problem.

The solution is called [liquibase](https://www.liquibase.org/).

### How to add liquibase

Firstly, add an *1.sql* file to the *resources/liquibase/changelog* folder with the following content:

```
--liquibase formatted sql

--changeset author:id logicalFilePath:path-independent splitStatements:false rollbackSplitStatements:false stripComments:false
PUT_SQL_CODE_HERE
```

Don't forget to change *PUT_SQL_CODE_HERE* and *author:id*.

Secondly, add a *changelog-master.xml* file to the *resources/liquibase* folder with the following content:

```
<?xml version="1.0" encoding="UTF-8"?>
<databaseChangeLog
        xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
                      http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-3.1.xsd">

    <include file="changelog/1.sql" relativeToChangelogFile="true"/>
</databaseChangeLog>
```

Thirdly, add a *init.sql* file with a schema initialization script, because liquibase can't init schema since it requires additional tables to store information beforehand.  

In the end, your liquibase project layout should look like this below:

```
resources/liquibase/changelog/1.sql - a .sql file
resources/liquibase/changelog-master.xml - it includes a path to a .sql file
resources/liquibase/init.sql - a .sql file with a schema initialization script
```

### How to add liquibase-maven plugin

Firstly, add a *liquibase.properties* file to the *resources/liquibase* folder with the following content:

```
changeLogFile: src/main/resources/liquibase/changelog-master.xml
driver: org.postgresql.Driver
url: jdbc:postgresql://localhost:5432/postgres?currentSchema=MY_SCHEMA_NAME
username: postgres
password: postgres
```

You may change it according to your database setting.

You could read more about how to configure *liquibase.properties* file [here](https://docs.liquibase.com/workflows/liquibase-community/creating-config-properties.html).

Secondly, add the following code below to the pom.xml to enable *[liquibase-maven plugin](https://mvnrepository.com/artifact/org.liquibase/liquibase-maven-plugin)*:

```
<build>
  <plugins>
    <plugin>
      <groupId>org.liquibase</groupId>
      <artifactId>liquibase-maven-plugin</artifactId>
      <version>...</version>
      <configuration>
        <propertyFile>src/main/resources/liquibase/liquibase.properties</propertyFile>
        <promptOnNonLocalDatabase>false</promptOnNonLocalDatabase>
      </configuration>
      <executions>
        <execution>
          <phase>process-resources</phase>
          <goals>
            <goal>update</goal>
          </goals>
        </execution>
      </executions>
    </plugin>
  </plugins>	
</build>
```

Don't forget to replace ... with the [actual version number](https://mvnrepository.com/artifact/org.liquibase/liquibase-maven-plugin).

### How to update a database with liquibase-maven plugin

If you want to update a database simply execute liquibase maven plugin *update* goal.

You could checkout the picture below:

![]({{ site.url }}/assets/2018-05-17-how-to-add-liquibase-to-the-existing-project-1.jpg)

### Conclusion

We have discussed how to add liquibase and liquibase-maven plugin to the existing project.

You could read more information about how to use liquibase [here](http://www.liquibase.org/quickstart.html), [my liquibase tips](https://vmaks.github.io/other/2018/08/09/liquibase-tips.html) and [checkout how to write SQL format rollback code](https://vmaks.github.io/other/2018/06/18/liquibase-sql-format.html).

We also have discussed how to update a database with liquibase-maven plugin.

You could check out the source code [here](https://github.com/vmaks/liquibase-example) as well.
