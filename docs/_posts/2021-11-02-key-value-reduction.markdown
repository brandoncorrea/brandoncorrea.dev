---
layout: post
title:  "Key-Value Reduction"
date:   2021-11-02 7:52:05 -0400
categories: clojure, apprenticeship
---

While I was working on the Coin Changer Kata today, I ran across a neat 
function, [reduce-kv][reduce-kv]! This is basically [reduce][reduce] for 
key-value collections with a couple important differences.

`reduce-kv` requires a default parameter–this is optional with `reduce`.
The function passed into `reduce-kv` also requires three parameters: 
the previous value and the key and value of the next item in the collection.

Applying a summation on a key-value collection using `reduce-kv` versus 
`reduce` may look something like this:

````clojure
=> (reduce-kv #(+ %1 %3) 0 {:a 1 :b 2 :c 3})
6
=> (reduce + (map second {:a 1 :b 2 :c 3}))
6
````

In cases like these, I think I'd favor the use of `reduce` for several reasons:
1. We don't use the key parameter
2. We can avoid the use of an anonymous function
3. We can get rid of the default value

If we wanted to do something like transform our collection's values, that's 
something we may prefer to use `reduce-kv` for.

````clojure
=> (reduce (fn [m [k v]] (assoc m k (range v))) {} {:a 1 :b 2 :c 3})
{:a (0), :b (0 1), :c (0 1 2)}
=> (reduce-kv #(assoc %1 %2 (range %3)) {} {:a 1 :b 2 :c 3})
{:a (0), :b (0 1), :c (0 1 2)}
````

This gives us a lot more options on how we handle problems around mapping and 
reducing key-value collections. It allows us to get around destructuring 
and indexing parameters and only show what actually matters.

[reduce-kv]: https://clojuredocs.org/clojure.core/reduce-kv
[reduce]: https://clojuredocs.org/clojure.core/reduce