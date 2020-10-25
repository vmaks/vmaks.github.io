---
layout: post
title:  "How to visualize OpenAPI (Swagger) with a Spring application"
date:   2019-10-15 00:30:00 +0300
tags: [Kotlin, Spring, Swagger]
---

If you want to visualize OpenAPI on a server then you need to use [swagger-ui](https://swagger.io/tools/swagger-ui/). Swagger-ui is a [React](https://reactjs.org/) application embedded in an html page. You need a webserver to serve it to a client.
The easiest way is to use either [Swagger UI Express](https://www.npmjs.com/package/swagger-ui-express) or [SpringFox](https://springfox.github.io/springfox/).

I am going to describe how to use SpringFox to visualize multiple swagger specifications. 

Firstly, you need to add dependencies to a pom file:

```
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>
```

Then add SwaggerConfig class to configure swagger.

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

    @Primary
    @Bean
    fun swaggerResourcesProvider(InMemorySwaggerResourcesProvider defaultResourcesProvider): SwaggerResourcesProvider {
        return SwaggerResourcesProvider {
            val resource = SwaggerResource()
            resource.name = "name example"
            resource.swaggerVersion = "2.0"
            resource.url = PATH_TO_SWAGGER_FILE_ON_THE_SERVER // Simply put a file into resources/static folder

            val resources = defaultResourcesProvider.get() // Add swagger auto generated documentation
            resources.add(resource);
            return resources;
        }
    }    
}
```

You could read more about how to configure swagger auto-generated documentation with the help of a Docket bean [here](https://vmaks.github.io/other/2018/11/11/how-to-use-swagger-to-document-rest-api-part2.html).

We need to configure a *SwaggerResourcesProvider* bean to be able to serve [multiple swagger specifications](http://springfox.github.io/springfox/docs/current/#aggregating-multiple-swagger-specifications-in-the-same-swagger-ui).
Then we create a *SwaggerResource* and set all required properties like *name, swaggerVersion and url*. The most important parameter is *url* because it is a path to a swagger specification on the server. I recommend putting all swagger specifications in the *resources/static* folder. You could get auto-generated documentation with the help of *defaultResourcesProvider.get()*. I use *@Primary* annotation to override the existing bean.
