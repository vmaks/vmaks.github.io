---
layout: post
title:  "How to use swagger to document REST API: Code first approach"
date:   2018-11-11 18:20:00 +0300
tags: [Kotlin, Spring, Swagger, REST]
---

### Code first approach

The approach is to implement all endpoints and models in a code and then expose REST API to a client or a developer in a company.

In the previous post I described [API first approach][14].

It much easier to implement the code first approach for an existing project compare to the API first approach, because you simply need to add a couple of dependencies and a configuration class. That's it!

However, code first approach is very useful because a frontend team will be able to get the current version of a backend API at any moment without bothering anyone from a backend team.

Add the following dependencies to a pom file:

```
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.7.0</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.7.0</version>
</dependency>
```

[*springfox-swagger2*][12] - it makes API avaliable in a JSON format.

[*springfox-swagger-ui*][13] - it makes API avaliable in a Swagger UI format.

Then add SwaggerConfig class to configure swagger. You could read more about it [here][11].

```
@Configuration
@EnableSwagger2
public class SwaggerConfig {                                    
    @Bean
    public Docket api() { 
        return new Docket(DocumentationType.SWAGGER_2)  
          .select()                                  
          .apis(RequestHandlerSelectors.any())              
          .paths(PathSelectors.any())                          
          .build();                                           
    }
}
```

*@EnableSwagger2* - it enables springfox-swagger2.

*new Docket* - it initialized springfox with SWAGGER_2 format.

*select()* - it returns an instance of ApiSelectorBuilder.

*apis()* - it allows to select a [RequestHandler][15].

*paths()* - it allows to select a [Path][16].

*build()* - it builds the selector after an api and a path configuraion.

Then open the following url to check the REST API in a JSON format:

http://localhost:8080/v2/api-docs

or in a  Swagger UI format:

http://localhost:8080/swagger-ui.html#/

Caution: you may need to change the default port.

You could use additional annotations like [@Api, @ApiOperation, @ApiResponses, @ApiResponse][7] to make you REST API more readable with help of [Swagger Annotations][8].

[1]: https://swagger.io/
[2]: https://editor.swagger.io/
[3]: https://github.com/swagger-api/swagger-codegen
[4]: https://repo1.maven.org/maven2/io/swagger/swagger-codegen-cli/
[5]: https://github.com/swagger-api/swagger-codegen#selective-generation
[6]: https://github.com/swagger-api/swagger-codegen/tree/master/modules/swagger-codegen-maven-plugin
[7]: https://github.com/swagger-api/swagger-core/wiki/Annotations-1.5.X
[8]: https://mvnrepository.com/artifact/io.swagger/swagger-annotations
[9]: https://github.com/swagger-api/swagger-codegen/issues/5451#issuecomment-366721082
[10]: https://github.com/AvaliaSystems/TrainingREST/issues/1
[11]: https://springfox.github.io/springfox/docs/current/#springfox-spring-mvc-and-spring-boot
[12]: https://mvnrepository.com/artifact/io.springfox/springfox-swagger2
[13]: https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui
[14]: https://vmaks.github.io/other/2018/11/05/how-to-use-swagger-to-document-rest-api-part1.html
[15]: https://github.com/springfox/springfox/blob/master/springfox-core/src/main/java/springfox/documentation/builders/RequestHandlerSelectors.java
[16]: https://github.com/springfox/springfox/blob/master/springfox-core/src/main/java/springfox/documentation/builders/PathSelectors.java
