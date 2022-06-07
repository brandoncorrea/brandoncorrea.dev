---
layout: post
title:  "Looking Past Existing Features"
date:   2022-02-22 7:52:05 -0400
categories: clojure, apprenticeship
---

We're often told to prefer for the simplest possible 
solution when writing code, but sometimes this can escape
our minds. Maybe the feature is so big that you have a
hard time visualizing what this would look like.

Today I was trying to figure out how I was going to 
implement a one-to-many relationship between entities in 
an application. We already have similar behavior with
another entity using a linked list, so naturally the
first option would be to implement it that way.

````clojure
;; The Linked List as a bag of rocks
(def bag
  {:id 10 :next-rock 1})

(def rocks
  [{:id 1 :next-rock 2}
   {:id 2 :next-rock 3}
   {:id 3 :next-rock nil}])
````

However, the problem with doing it this way is that the
original solution comes with a lot of overhead. There's
already so much logic built into the application for 
that linked list, and it would all need to change.

Another option was to create a join table–a completely
different structure than the linked list. This would
be cleaner and more supportable for the future, but
would miss the iteration. 

````clojure
(def bag
  {:id 10})

(def rocks
  [{:id 1} {:id 2} {:id 3}])

; The Join Entity
(def rock-bag
  {:id 20 :bag-id 10 :rocks [1 2 3]})
````

I found myself stuck–there's no way either of these 
solutions would make the iteration. So I contacted the
client to let him know that this story will miss the
iteration, and we started talking about what he wanted
for this story.

The story originally was to create a one-to-many 
relationship between two entities. No _lists_ were
actually mentioned–that was something I added on to
the story because of the behavior with a different 
feature!

So, could the solution be simpler than I originally 
thought? Is this just a matter of adding an ID to the 
"many" entity? 

````clojure
(def bag 
  {:id 10})

(def rocks 
  [{:id 1 :bag 10}
   {:id 2 :bag 10}
   {:id 3 :bag 10}])
````

It would be an unordered list, but order doesn't exactly 
matter at this point. The goal is to associate one entity
with another.
