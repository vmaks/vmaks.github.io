---
layout: post
title:  "How to deploy a java docker app with a database into the cloud"
date:   2020-05-10 16:03:00 +0300
categories: other
---

### Overview 

There are lots of popular cloud services nowadays. We are going to review one of them. It's [Heroku](https://www.heroku.com/).

In the post, we are going to discuss how to deploy a java docker app with a postgres database into the Heroku cloud and locally with the help of the *docker-compose*.

### Create a java docker application

We are going to take [java-getting-started](https://github.com/heroku/java-getting-started) application and create a docker image

Firstly, we need to compile the application with the help of the following command:

```
mvn clean install
```

Secondly, we have to create a Dockerfile:

```
FROM openjdk:8-jdk-alpine

COPY ./target/java-getting-started-1.0.jar java-getting-started-1.0.jar

# Run the image as a non-root user
RUN adduser -D myuser
USER myuser

EXPOSE 8080

CMD java $JAVA_OPTS -jar java-getting-started-1.0.jar
```

*openjdk:8-jdk-alpine* - you use any other base java 8 and higher image as well.

*COPY ./target/java-getting-started-1.0.jar java-getting-started-1.0.jar* - we copy jar from the target folder into the image.

*RUN adduser -D myuser
 USER myuser* - we use it because Heroku images are run [as a non-root user](https://devcenter.heroku.com/articles/container-registry-and-runtime#run-the-image-as-a-non-root-user). 

*EXPOSE 8080* - we use it for local deploy. However, Heroku [doesn't recommend exposing any port in docker image](https://devcenter.heroku.com/articles/container-registry-and-runtime#dockerfile-commands-and-runtime).

*CMD java $JAVA_OPTS -jar java-getting-started-1.0.jar* - [Heroku forces to use CMD](https://devcenter.heroku.com/articles/container-registry-and-runtime#dockerfile-commands-and-runtime).

### Build and deploy a java docker application into the cloud

We are going to use a [Heroku Postgres](https://www.heroku.com/postgres) as a database.

Firstly, we have to use the following commands to deploy the application.

```
# Log in to Container Registry
heroku container:login

# Create a Heroku app
heroku create

# Build the image and push to Container Registry
heroku container:push web
```

Secondly, we have to get the credentials for the database with the help of the following command:

```
heroku pg:credentials:url
```

You also could get the information about the database in the [data dashboard](https://data.heroku.com/).

Thirdly, we need to pass database settings as an environment variables with the help of Heroku [Config Vars](https://devcenter.heroku.com/articles/config-vars).

Simply add database settings into the Config Var section in the [application dashboard](https://dashboard.heroku.com/apps/).

Look at the example below:

![]({{ site.url }}/assets/2020-05-11-how-to-deploy-a-java-docker-app-with-a-database-in-heroku-1.jpg)

Fourthly, we are going to deploy the docker image into the cloud with the help of the following command:

```
heroku container:release web
```

Lastly, we are going to check the application with the help of the following command:

```
heroku open
```

### Build and deploy locally with the help of docker-compose

I recommend using [*docker-compose*](https://docs.docker.com/compose/) for local deploy.

Simply, put in the root folder *docker-compose.yml* file with the following content:

```
version: '3'
services:
  db:
    image: postgres:9.6
    environment:
      POSTGRES_PASSWORD: postgres
    ports:
      - 5432:5432
  app:
    build: .
    ports:
      - 8080:8080
    environment:
      - JDBC_DATABASE_URL=jdbc:postgresql://db:5432/postgres
      - DATABASE_USERNAME=postgres
      - DATABASE_PASSWORD=postgres
      - PORT=8080
``` 

In the *docker-compose.yml* file you could add a local database as db and define all required environment variables for the app.

To run the app simply use the following command:

```
docker-compose up -d
``` 

### Conclusion

We have discussed how to deploy a java docker app with a postgres database into the Heroku cloud and locally with the help of the *docker-compose*.
You could check out the source code [here](https://github.com/vmaks/java-getting-started).
