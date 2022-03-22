---
layout: post
title:  "Reverse Sorting"
date:   2022-03-21 7:52:05 -0400
categories: clojure apprenticeship
---

I've been looking for ways to sort items in reverse order
while maintaining Clojure's laziness. So first, let's look
at the straightforward, unlazy way of reverse sorting.

````clojure
=> (reverse (sort [1 3 2 6 4]))
(6 4 3 2 1)
````

This sort form will order the collection in ascending order 
first, then reverse that sorted collection. So it will have
to go over the entire collection again after it's sorted. 

But what if we only want the first item in the list? We 
don't need to go over the entire list for this, or even
sort the whole thing for that matter. We just need to 
sort by the complement of the default comparer.

There are a couple different ways to go about this. 
We can either negate the result of the initial call to
`compare` or pass the parameters into `compare` in 
reverse order.

````clojure
(def numbers [1 3 2 6 4])
(sort #(compare %2 %1) numbers)
(sort #(- (compare %1 %2)) numbers)
````

I personally prefer reversing the order of the parameters,
since it's just one less thing for the processor to do.

The same way we can reverse `sort`, we can also reverse `sort-by`!

````clojure
(def pets 
  [{:name "Fluffy" :age 3}
   {:name "Dog" :age 5}
   {:name "Lucky" :age 1}])

(sort-by :age #(compare %2 %1) pets)
````

And to clean this up a bit...

````clojure
(defn contrast [x y] (compare y x))
(defn rsort [coll] (sort contrast coll))
(defn rsort-by [key-fn coll] (sort-by key-fn contrast coll))

(rsort numbers)
(rsort-by :age pets)
````
