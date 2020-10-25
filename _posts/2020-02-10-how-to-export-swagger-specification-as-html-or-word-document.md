---
layout: post
title:  "How to export swagger specification as html or word document"
date:   2020-02-10 00:13:00 +0300
tags: [Swagger, OpenAPI, Maven]
---

### Overview

[Swagger and OpenAPI Specification](https://swagger.io/docs/specification/about/) is a great way to describe RESTful interfaces.
[We write OpenAPI definitions in YAML or JSON](https://swagger.io/docs/specification/basic-structure/).
Sometimes you may need to export OpenAPI definitions into html or word document.
In the post, we are going to discuss how to export swagger specification as html or word document.

### Convert OpenAPI definitions into html document

We use [*swagger2markup-maven-plugin*](https://mvnrepository.com/artifact/io.github.swagger2markup/swagger2markup-maven-plugin) to convert OpenAPI definitions from YAML or JSON to [ASCIIDOC](http://asciidoc.org/) format.
 
Then we use [*asciidoctor-maven-plugin*](https://mvnrepository.com/artifact/org.asciidoctor/asciidoctor-maven-plugin) to convert ASCIIDOC into html document.

Simply add *swagger2markup-maven-plugin* and *asciidoctor-maven-plugin* to the pom.xml file:

```
<project ...>    

    ...

    <build>
        <plugins>
            <plugin>
                <groupId>io.github.swagger2markup</groupId>
                <artifactId>swagger2markup-maven-plugin</artifactId>
                <version>${swagger2markup.plugin.version}</version>
                <dependencies>
                    <dependency>
                        <groupId>io.github.swagger2markup</groupId>
                        <artifactId>swagger2markup-import-files-ext</artifactId>
                        <version>${swagger2markup.extension.version}</version>
                    </dependency>
                    <dependency>
                        <groupId>io.github.swagger2markup</groupId>
                        <artifactId>swagger2markup</artifactId>
                        <version>${swagger2markup.version}</version>
                    </dependency>
                </dependencies>
                <configuration>
                    <swaggerInput>${SWAGGER_INPUT_FILE_PATH}</swaggerInput>
                    <outputDir>${generated.asciidoc.directory}</outputDir>
                    <config>
                        <swagger2markup.markupLanguage>ASCIIDOC</swagger2markup.markupLanguage>
                        <swagger2markup.pathsGroupedBy>TAGS</swagger2markup.pathsGroupedBy>
                    </config>
                </configuration>
                <executions>
                    <execution>
                        <phase>generate-sources</phase>
                        <goals>
                            <goal>convertSwagger2markup</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>

            <plugin>
                <groupId>org.asciidoctor</groupId>
                <artifactId>asciidoctor-maven-plugin</artifactId>
                <version>${asciidoctor.maven.plugin.version}</version>
                <configuration>
                    <sourceDirectory>${ASCIIDOC_INPUT_FOLDER_PATH}</sourceDirectory>
                    <sourceDocumentName>${ASCIIDOC_INPUT_FILE_NAME}</sourceDocumentName>
                    <attributes>
                        <doctype>book</doctype>
                        <toc>left</toc>
                        <toclevels>3</toclevels>
                        <numbered></numbered>
                        <hardbreaks></hardbreaks>
                        <sectlinks></sectlinks>
                        <sectanchors></sectanchors>
                        <generated>${ASCIIDOC_OUTPUT_FOLDER_PATH}</generated>
                    </attributes>
                </configuration>
                <executions>
                    <execution>
                        <id>output-html</id>
                        <phase>generate-resources</phase>
                        <goals>
                            <goal>process-asciidoc</goal>
                        </goals>
                        <configuration>
                            <backend>html5</backend>
                            <outputDirectory>${HTML_OUTPUT_FOLDER_PATH}</outputDirectory>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>    
```

### Convert html into docx document

Then we use [pandoc](https://pandoc.org/) to convert html to docx document.
 
Simply use the following command:

```
pandoc.exe --from html --to docx -o definitions.docx index.html
```

Pandoc is a swiss-army knife so you can convert to and from different file formats.

You could find out more with the following command:

```
pandoc.exe -h
```

### Conclusion

We have described how to export swagger specification as html or word document.

Links:

https://github.com/Swagger2Markup/swagger2markup-maven-project-template
