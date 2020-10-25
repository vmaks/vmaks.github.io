---
layout: post
title:  "How to write data in CSV format"
date:   2020-06-25 00:39:00 +0300
tags: [Kotlin, CSV]
---

### Overview

Sometimes we need to save data from a database in a file. 
For example, we could write data in a [CSV format](https://en.wikipedia.org/wiki/Comma-separated_values).
In the post, we are to use [OpenCSV library](http://opencsv.sourceforge.net/) to write data in CSV format.

### OpenCSV usage example

Firstly, add [OpenCSV library from maven repository](https://mvnrepository.com/artifact/com.opencsv/opencsv).

Secondly, create *CSVWriter* with a file name as argument:

```
val writer = CSVWriter(FileWriter(fileName))
```

Thirdly, you could use *writer* to write data:

```
writer.writeNext(row.toTypedArray())
```

You could see the whole example below:

```
import com.opencsv.CSVWriter
import java.io.FileWriter

fun main() {
    val fileName = "example.csv"
    val writer = CSVWriter(FileWriter(fileName))
    val rows = listOf(
        listOf("name", "age"),
        listOf("Mark", "20"),
        listOf("Jane", "22")
    )
    writer.use {
        for (row in rows) {
            writer.writeNext(row.toTypedArray())
        }
    }
}
```

### Other CSV libraries

You could find more information about other CSV libraries [here](https://www.baeldung.com/java-csv#third-party-libraries).

### Conclusion

We have discussed how to write data in CSV format.
You could check out the source code [here](https://github.com/vmaks/csv-example).
