---
layout: post
title:  "How to create a simple Spring server application with React frontend"
date:   2020-05-17 21:04:00 +0300
tags: [Spring, Kotlin, React, Javascript]
---

### Overview

In the post, we are going to create a simple [Spring](https://spring.io/) server application with [Gradle build system](https://gradle.org/) with [React](https://reactjs.org/) frontend.

### Hello World server application

Firstly, we are going to create a simple Java server application.

We use [Spring initializr project](https://start.spring.io/) to create a simple Spring application with Spring Web dependency.

We use Kotlin as a language and Gradle as a build system for the project.

### Hello World frontend application

Secondly, we are going to create a simple [React](https://reactjs.org/docs/create-a-new-react-app.html) frontend application.

To accomplish it do the following steps:

* Install https://nodejs.org/en/
* Run the following script in the root folder of a project:

```
npx create-react-app <app-name>
```

* Run the following script in the <app-name> folder to check it:

```
npm start
```

* Build the React app with the following command:

```
npm run build
```

* Put the result of it from the *build* folder into *resources/static* folder of the Spring boot application.

* Run the spring boot application and check the result.

### Conclusion

We have created a simple Spring server application with React frontend. 
You could check out the source code [here](https://github.com/vmaks/github-actions-helloworld).
