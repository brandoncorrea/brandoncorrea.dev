---
layout: post
title:  "Destructuring Collections"
date:   2021-11-18 7:52:05 -0400
categories: clojure, apprenticeship
---

The idea of destructuring parameters was confusing to me at first,
but after seeing it used a few times, it started to make a lot more sense.

Today I revisited some of the [Clojure Koans][clojure-koans] and came 
across the destructuring meditation.

### Destructuring Collections

The most basic type of destructuring is just taking some number of values
from a collection. If I wanted a function to add the first two elements, 
I could write something like this:

````clojure
user=> (defn add-first-two [[a b]] (+ a b))
#'user/add-first-two
user=> (add-first-two [1 2])
3
user=> (add-first-two '(3 2 1))
5
````

Using a second set of brackets in the function's parameter list, 
I can pass any collection to the function, then name and use 
the first two values of that collection.

Without destructuring, I'd be left with something like this:

````clojure
(defn add-first-two [coll] 
  (+ (first coll) (second coll)))
````

### The `& Rest` of the List

So what if we have a _really_ long list, or a list where we don't
know the size ahead of time? Using `& rest` parameters, we can 
store the remainder of the list in a sublist for later use.

Here is a light version of `or` using the rest parameter.

````clojure
user=> (defn my-or [[next & rest]]
         (if next next (my-or rest)))
#'user/my-or
user=> (my-or [nil false 1 true])
1
````

Using the rest parameter, we can take only the number of items 
we need from the collection and store the `rest` of the list in another 
variable! 

Though, the rest parameter doesn't necessarily _need_ to be named
`rest`. You can call it `flowers`, `lightsabers`, or something that makes
a little more sense with the context of your function. 

### Restructuring Collections

One thing I didn't know about destructuring is that you can have both 
destructured and fully structured values in your parameter list!

To elaborate a bit more... If I have a collection, `[1 2 3]`, and
want to use these items both separately and jointly, `1 2 3` and `[1 2 3]`, 
I can do so without the extra work of breaking them down or 
joining them together in the function body.

````clojure
user=> (defn sum-description [[a b :as operands]] 
         (str "The sum of " operands " is " (+ a b)))
#'user/sum-description
user=> (sum-description [123 456])
"The sum of [123 456] is 579"
````

One thing to be aware of is that while the left part of `:as` holds
the destructured parameters, `a b`, the right part, `operands`, will 
hold the entirety of the parameter.

If I decided to pass more than two numbers into this function, then 
the result wouldn't make much sense.

````clojure
user=> (sum-description [123 456 789])
"The sum of [123 456 789] is 579"
````

There's a ton more than can be done with destructuring as well,
like destructuring map values! But I'll leave that for another post.

[clojure-koans]: https://github.com/functional-koans/clojure-koans