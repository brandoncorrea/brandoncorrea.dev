---
layout: post
title:  "Anonymous Types in Clojure"
date:   2021-12-20 7:52:05 -0400
categories: clojure, apprenticeship
---

I have some Java code that I'm trying to get my Clojure code 
to work with, but one of the main functions require an argument of
type `java.util.function.Function`.

I had originally thought that maybe Clojure functions would 
automatically conform to the Function interface, but this is not the
case. So how do we handle this situation?

One option is to use `deftype` to implement the Function interface,
which just has one method: `apply`

````clojure
(deftype Func [f]
  Function (apply [_ arg] (f arg)))

(defn say-hi [greeting]
  (JavaClass/doSomething 
    (->Func #(str greeting " " % ", from deftype!"))))

=> (say-hi "Hello")
"Hello World, from deftype!"
````

However, creating a new type seems like a bit much for our use.
Func is really just a wrapper for the Function interface. Instead,
what we could use here is `reify` to make our function look like a
Java function.

````clojure
(defn say-hi [greeting]
  (JavaClass/doSomething
    (reify Function 
      (apply [_ name] (str greeting " " name ", from reify!")))))

=> (say-hi "Greetings")
"Greetings World, from reify!"
````

There doesn't seem to be much of a difference between `reify` and 
`deftype`, except that reify can be used inline while deftype
must be defined outside the function.

This can be very useful when testing a function that depends on 
another interface. It allows you to mock out different scenarios
without having to create tons of deftypes. 

Or suppose you only needed to use a type for one part of your code,
and never used that type again. This would be a good use for reify.

At the very least, if you needed a private type, you'd be able to
do so with `defn-` `reify`.
