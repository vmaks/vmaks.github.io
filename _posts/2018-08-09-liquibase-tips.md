---
layout: post
title:  "Liquibase Tips"
date:   2018-08-09 22:40:00 +0300
categories: other
---

If you haven't heard about [liquibase][0] yet then I will explain it shortly:

it is a tool that helps you manage all database changes very easily.

- Use maven/gradle plugin to update dev database

- Store all liquibase related data in a resources folder

```
resources/liquibase/changelog/1.sql - a .sql file
resources/liquibase/changelog-master.xml - it inludes path to .sql files
resources/liquibase/liquibase.yml - liquibase settings: connection to a database, etc
```

- Use *relativeToChangelogFile="true"* attribute in *changelog-master.xml* to easily move liquibase files if required.

*changelog-master.xml*

```
<?xml version="1.0" encoding="UTF-8"?> 
<databaseChangeLog
  xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
                      http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-3.1.xsd">

  <include file="changelog/1.sql" relativeToChangelogFile="true"/>
  <!-- ... -->
</databaseChangeLog>
```

- Use [liquibase SQL format][1] and write you own rollback statements, because it's ease to write, check and reuse in future.

```
--liquibase formatted sql

--changeset author:id
CREATE TABLE order (
...
);

--rollback drop table order;
```

- Use [*logicalFilePath:path-independent*][3] to disable FILENAME column liquibase check

```
--liquibase formatted sql

--changeset author:1 logicalFilePath:path-independent
CREATE TABLE order (
...
);
```

- Use *splitStatements:false* for a changeset and [*rollbackSplitStatements:false*][2] for a rollback multiple statements

```
--liquibase formatted sql

--changeset author:id splitStatements:false rollbackSplitStatements:false
```

- Use *stripComments:false* for a changeset if you have comments in sql code

```
--liquibase formatted sql

--changeset author:id stripComments:false
```

- You could execute any liquibase command via a liquibase console application like in the example for Postgres database below:

```
java -jar liquibase.jar --url=jdbc:postgresql://DATABASE_HOST:DATABASE_PART/DATABASE_NAME?currentSchema=SCHEMA_NAME --changeLogFile=./PATH_TO_CHANGELOG_FOLDER/changelog-master.xml --username=DATABASE_USERNAME --password=DATABASE_PASSWORD --driver=org.postgresql.Driver --classpath=postgresql-VERSION.jar --verbose updateSQL
```

Notice that you have to add a postgresql-VERSION.jar file.

- I don't recommend using 4.0.0 Installer for Windows, liquibase-4.0.0.zip or liquibase 3.10.2.tar.gz on Windows platform, because it could lead to changeset hash sum errors due to encoding issues. I recommend using a maven or Gradle plugin on the Windows platform and maven or Gradle plugin, and console application liquibase 3.10.2.tar.gz on the Linux platform.

- You could read more about liquibase best practices [here](http://www.liquibase.org/bestpractices.html)

[0]: https://www.liquibase.org

[1]: https://www.liquibase.org/documentation/sql_format.html

[2]: https://github.com/liquibase/liquibase/pull/334

[3]: https://stackoverflow.com/a/19959756/3001953
