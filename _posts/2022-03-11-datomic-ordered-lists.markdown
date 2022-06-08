---
layout: post
title:  "Datomic Ordered Lists"
date:   2022-03-11 7:52:05 -0400
categories: clojure, apprenticeship
---

In Datomic, you can find ways to make one-to-many associations
between two entities, but what about an ordered one-to-many
association?

One of the most basic data structures for ordered collections 
is an array.

````clojure
(def friend-ids [4 3 8])
````

However, Datomic unfortunately doesn't store array types.
These get turned into sets, which are unordered and only 
hold unique values.

Another option is using a linked list which, as I talk about
in [another post][linked-lists], is more complicated and 
has a bit more overhead that comes with it. 

This leaves us with a stringified vector.

````clojure
(def friends "[4 3 8]")
````

While this may not be the most efficient data structure as
far as memory and performance, it's simple to implement, 
and therefore less prone to bugs. 

However, with a stringified vector, we lose referential 
integrity. This means that we cannot guarantee that every ID 
in the vector actually exists, so we'd need to account for 
nil cases when working with the items in the collection.

Still, I think if the Linked List can be hidden behind a 
stable list interface that doesn't live on the entities 
themselves but in its own entity, this would *probably* 
be the best option.

[linked-lists]: https://brandoncorrea.dev/clojure/apprenticeship/2022/02/16/linked-lists-are-painful.html