---
layout: post
title:  "Clojure is REALLY Lazy"
date:   2022-02-23 7:52:05 -0400
categories: clojure, apprenticeship
---

You may have heard that Clojure is lazy. This
basically means that Clojure won't evaluate an expression
unless it absolutely needs to–simple enough, right?

Well, today I experienced a new limit in Clojure's laziness!

### Take Only What You Need

In Clojure, you can `map` and `filter` over an infinite
sequence, but only receive the value you need!

So something like this will only evaluate values until 
an even number is found. If the first value is even,
then only one value will be evaluated. If it is the
then number, then only ten values. Or in the very
unfortunate case where the only even number is the 
last number, then one-trillion values will be evaluated.

````clojure
(def one-trillion-random-numbers0
  [#_...])

(defn first-even-random []
  (first (filter even? one-trillion-random-numbers)))
````

Similarly, if you map over a sequence of values,
only those requested will be evaluated! Here,
we only increment the first ten numbers in the 
infinite sequence, because those are the only
numbers we care about.

````clojure
(def some-numbers (take 10 (map inc (range))))
````

### Do Seq What You Must

So, what happens if we do something like this?

````clojure
(defn send-promotions-to-users [promotions]
  (map db/create-promotion! promotions)
  (map db/send-promotions! (db/users)))
````

Well, I'd expect that this function would create some 
promotions in the database, then send those newly created
promotions out to all the users...wrong!

We can say one thing for certain about this function:
it will never create any promotions. `map` is _so_ lazy
that it will completely skip over mapping the promotion 
collection, because nothing receives its product on the 
other end. Depending on how this function is called, it
may even skip sending the promotions as well!

So how can we ensure that promotions are both created
_and_ delivered to users? For this, we'll need to 
use `doseq`.

````clojure
(defn send-promotions-to-users [promotions]
  (doseq [promo promotions] (db/create-promotion! promo))
  (doseq [user  (db/users)] (db/send-promotions! user)))
````

I just spent about ten minutes trying to figure out why
my test was failing when I "knew" I was updating all
of my database entities (using `map`). So keep an eye
out for those lazy functions if you _need_ something to
happen.