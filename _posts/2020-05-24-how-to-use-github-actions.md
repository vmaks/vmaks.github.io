---
layout: post
title:  "How to use GitHub Actions"
date:   2020-05-24 23:00:00 +0300
categories: other
---

### Overview

In the post, we are going to review GitHub Actions.

### GitHub Actions Overview

Firstly, we are going to look at GitHub Actions. It is a CI and CD engine from GitHub.

It is very easy to set up a GitHub Action pipeline if you have a project in GitHub.

Simply click on the *Actions tab*. Then click on the *New workflow* button.

You could either create a *Simple workflow* or choose from one of the existing workflows. 

In the end, you will end up with a yml file in the *.github/workflows* folder.

You could create [multiple workflows](https://help.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows), [customise them](https://help.github.com/en/actions/reference/workflow-syntax-for-github-actions) and [reuse actions](https://help.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#referencing-actions-in-your-workflow) as well.

### GitHub Actions Hello World

We are going to build a Spring application with Gradle build system and with React frontend.

Add a *build.yml* file to the *.github/workflows* folder with the following content:

```
name: Build

on:
  push:
    branches:
      - master

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      - name: Use Node.js 12.x
        uses: actions/setup-node@v1
        with:
          node-version: 12.x
      - run: cd demo-frontend && npm install
      - run: cd demo-frontend && npm run build
      - run: cp -a demo-frontend/build/. demo-server/src/main/resources/static/
      - name: Set up JDK 1.8
        uses: actions/setup-java@v1
        with:
          java-version: 1.8
      - name: Cache Gradle packages
        uses: actions/cache@v1
        with:
          path: ~/.gradle/caches
          key: ${{ runner.os }}-gradle-${{ hashFiles('**/*.gradle') }}
          restore-keys: ${{ runner.os }}-gradle
      - name: Grant execute permission for gradlew
        run: cd demo-server && chmod +x gradlew
      - name: Build with Gradle
        run: cd demo-server && ./gradlew build
```

*name: Build* - the name of the workflow

*on:* - [you could choose what is going to trigger the workflow](https://help.github.com/en/actions/reference/workflow-syntax-for-github-actions#on). In the example above it is *push* to the *master* branch.

*jobs:* - [it composes the workflow](https://help.github.com/en/actions/reference/workflow-syntax-for-github-actions#jobs)

*runs-on: ubuntu-latest* - [set a system to run on](https://help.github.com/en/actions/reference/workflow-syntax-for-github-actions#jobsjob_idruns-on). You could choose even Mac OS;)

*steps:* - [a sequence of tasks](https://help.github.com/en/actions/reference/workflow-syntax-for-github-actions#jobsjob_idsteps) execute by a workflow.

You could use a simple bash command like 

```- run: cd demo-frontend && npm install```
 
or [reuse an existing action](https://help.github.com/en/actions/reference/workflow-syntax-for-github-actions#jobsjob_idstepsuses) with the help of *uses*.

For example a command *uses: actions/checkout@v2** helps you to check out the source code.

There are lots of helpful actions in [the marketplace](https://github.com/marketplace) such as [cache dependencies for gradle](https://help.github.com/en/actions/language-and-framework-guides/building-and-testing-java-with-gradle#caching-dependencies) and [deploy docker image to heroku cloud](https://github.com/marketplace/actions/deploy-docker-image-to-heroku-app).

You also could [add a badge](https://help.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#adding-a-workflow-status-badge-to-your-repository. The easiest way to do is the following: *Actions/Choose workflow/Create status badge*.

In the end, it's a very easy and convenient way to build and test code on Github platform.

### Conclusion

We have reviewed GitHub Actions. You could check out the source code [here](https://github.com/vmaks/github-actions-helloworld).
