---
layout: post
title:  "How to handle POST request failure"
date:   2020-10-10 15:36:00 +0300
categories: other
---

### Overview

We use a [POST request](https://www.restapitutorial.com/lessons/httpmethods.html) to create an object via [REST API](https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm). 
POST request [is neither safe nor idempotent](https://www.restapitutorial.com/lessons/httpmethods.html).
For example, it may fail due to client timeout or an internal server error. 
It is therefore recommended for non-idempotent resource requests. 
Making two identical POST requests will most likely result in two resources containing the same information.
Usually, the object is saved in a database.
In most cases, we want to guarantee the avoidance of a duplicate in a database.
In the post, we are going to review different approaches to dealing with the problem. 

### POST once

In the *POST once* approach we have to guarantee that a POST request with the same data could be handled only once by a check.
We could use a unique client ID to identify duplicate POST requests and notify the client about it in the response if needed. 
If an object exists in a database, the server will throw an exception.

You could read more about the approach [here](https://stackoverflow.com/a/55026313/3001953).

### POST-PUT pattern

In the *POST-PUT pattern* approach we use a two-stage process of object creation.
The client has to follow the process to create an object. 
Firstly, the client sends a POST request with no data to book a unique server ID.
Secondly, the client sends a [PUT request](https://www.restapitutorial.com/lessons/httpmethods.html) with data and the server ID from the previous step.
The client has to send as many PUT requests as needed until it succeeds because the PUT request is idempotent.
Also, the server has to create an empty object for a POST request and implement a clean job. 
The job periodically cleans a database from empty objects.

You could read more about the approach [here](http://restalk-patterns.org/post-put.html) and [here](https://stackoverflow.com/a/51515064/3001953).
  
### Long-Running Operation with Polling pattern

In the *Long-Running Operation with Polling pattern* approach we use polling to avoid client timeouts when waiting for long-running operation results.
Firstly, the server persists the request data into a database, or a message broker to avoid the loss of data.
Secondly, the server responses with a unique job id to the client.
Thirdly, the server executes the job and saves the result into a database.
Fourthly, the client periodically checks the result of the job by using the job id.

The server has to periodically clean up the job result later.

You could read more about the approach [here](http://restalk-patterns.org/long-running-operation-polling.html)

### Callback or webhook

In the *Callback or webhook* approach the client pass in the request a return URL that the server API can call to let the client know that the work has been completed.
Usually, the server persists the request data into a database, or a message broker to avoid the loss of data similar to the *Long-Running Operation with Polling pattern*. 

You could read more about the approach [here](https://dzone.com/articles/rest-callbacks) and [here](https://stackoverflow.com/a/51514915/3001953).

### Conclusion

We have described different approaches to dealing with the POST request failure.
