---
layout: post
title:  "Hibernate relationships"
date:   2020-03-20 22:40:00 +0300
tags: [Hibernate]
---

### Overview

There are six types of hibernate relationships: 
one-to-one [bidirectional](https://vmaks.github.io/other/2020/04/27/hibernate-one-to-one-bidirectional-relationship.html)/[unidirectional](https://vmaks.github.io/other/2020/05/04/hibernate-one-to-one-unidirectional-relationship.html), 
many-to-many [bidirectional](https://vmaks.github.io/other/2020/04/05/hibernate-many-to-many-bidirectional-relationship.html)/[unidirectional](https://vmaks.github.io/other/2020/04/12/hibernate-many-to-many-unidirectional-relationship.html) 
and many-to-one [bidirectional](https://vmaks.github.io/other/2020/03/30/hibernate-many-to-one-bidirectional-relationship.html)/[unidirectional](https://vmaks.github.io/other/2020/03/23/hibernate-many-to-one-unidirectional-relationship.html).
In the post, we are going to discuss the difference between different types of hibernate relationships.

### The difference between bidirectional and unidirectional relationship

It's very important to understand the difference between bidirectional and unidirectional relationships.

In the case of a bidirectional relationship, you have access to the other side of the relationship from both sides of the relationship.

On the other hand, in case of a unidirectional relationship you have access to the other side of the relationship only from one side.

We are talking only about Java class or so-called Entity because in the database both bidirectional/unidirectional relationship looks the same.

### Conclusion

We have discussed the difference between different types of hibernate relationships.
We are going to discuss each pair of relationships in the following posts. 
