---
layout: post
title:  "How to use swagger to document REST API: API first approach"
date:   2018-11-05 19:56:00 +0300
tags: [Maven, OpenAPI | Swagger, REST]
---

[Swagger][1] is a tool that helps you create and document your REST API.

By REST API I mean endpoints and models. 
By endpoint I mean get, post, put, delete handlers. 
By model I mead data that pass to and return by an endpoint.

There are two main ways to use [swagger][1] in a project:

- API first approach

- Code first approach

### API first approach

The approach is to describe all REST API in a file with help of an [editor][2] and then generate all handlers and models for you preferable programming language with help of a [swagger-codegen][3] tool.

Let's define a swagger file (box.yaml) with an endpoint and a model

```
swagger: "2.0"
info:
  version: "1.0.0"
  title: "REST API example"
paths:
  /box:
    get:
      summary: "get a box"
      description: ""
      produces:
      - "application/json"
      responses:
        200:
          description: "successful operation"
          schema:
            $ref: "#/definitions/Box"
definitions:
  Box:
    type: "object"
    properties:
      id:
        type: "integer"
        format: "int64"
      height:
        type: "integer"
        format: "int32"
      width:
        type: "integer"
        format: "int32"
```

You can download swagger-codegen from [here][4].

Then run it with help of the following command:

```
java -jar swagger-codegen-cli-2.3.1.jar generate -i box.yaml -l spring -o ./tmp/java
```

It specifies the name of a file with REST API (*-i box.yaml*), a programming language (*-l spring*) and an output directory with generated code (*-o /tmp/java*).

You can selectively generate only models (*-Dmodels*), API (*-Dapis*) or both (*-Dmodels -Dapis*) with help of the following [flags][5].

For example to generate only models and API you can use the following flags:

```
-Dmodels -Dapis
```

You could [generate only interfaces][9] with help of the following command: 

```
-DinterfaceOnly=true
```

The result command may look like this:

```
java -jar swagger-codegen-cli-2.3.1.jar generate -i box.yaml -l spring -o ./tmp/java_model_api -Dmodels -Dapis -DinterfaceOnly
=true
```

The following command will show all avaliable options for a language [https://github.com/swagger-api/swagger-codegen]:

```
java -jar swagger-codegen-cli-2.3.1.jar config-help -l {lang}
```

The following command will show all avaliable languages:

```
java -jar swagger-codegen-cli-2.3.1.jar langs
```

You can generate code automatically with help of a [swagger-codegen-maven-plugin][6] if you want.

Simply add the following [piece code][6] to a pom file:

```
<dependencies>
    <!-- ... -->

    <dependency>
        <groupId>io.swagger</groupId>
        <artifactId>swagger-annotations</artifactId>
        <version>1.5.21</version>
    </dependency>
</dependencies>

<build>
    <plugins>
        <!-- ... -->
        
        <plugin>
            <groupId>io.swagger</groupId>
            <artifactId>swagger-codegen-maven-plugin</artifactId>
            <version>2.3.1</version>
            <executions>
                <execution>
                    <goals>
                        <goal>generate</goal>
                    </goals>
                </execution>
            </executions>
            <configuration>
                <inputSpec>${project.basedir}/src/main/resources/box.yaml</inputSpec>
                <language>spring</language>
                <configOptions>
                    <sourceFolder>src/gen/java/main</sourceFolder>
                    <interfaceOnly>true</interfaceOnly>
                    <java8>false</java8>
                </configOptions>
                <generateApis>true</generateApis>
                <generateApiTests>false</generateApiTests>
                <generateApiDocumentation>false</generateApiDocumentation>
                <generateModels>true</generateModels>
                <generateModelTests>false</generateModelTests>
                <generateModelDocumentation>false</generateModelDocumentation>
                <generateSupportingFiles>false</generateSupportingFiles>
            </configuration>            
        </plugin>
    </plugins>
</build>
```

Code first approach will be described in [the next post][10].

[1]: https://swagger.io/
[2]: https://editor.swagger.io/
[3]: https://github.com/swagger-api/swagger-codegen
[4]: https://repo1.maven.org/maven2/io/swagger/swagger-codegen-cli/
[5]: https://github.com/swagger-api/swagger-codegen#selective-generation
[6]: https://github.com/swagger-api/swagger-codegen/tree/master/modules/swagger-codegen-maven-plugin
[7]: https://github.com/swagger-api/swagger-core/wiki/Annotations-1.5.X
[8]: https://mvnrepository.com/artifact/io.swagger/swagger-annotations
[9]: https://github.com/swagger-api/swagger-codegen/issues/5451#issuecomment-366721082
[10]: https://vmaks.github.io/other/2018/11/11/how-to-use-swagger-to-document-rest-api-part2.html
