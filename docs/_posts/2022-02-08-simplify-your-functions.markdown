---
layout: post
title:  "Simplify Your Functions"
date:   2022-02-08 7:52:05 -0400
categories: clojure apprenticeship
---

I was working on a math problem today trying to determine
how many seconds it would take for an object to reach the ground 
from the top of the Eiffel Tower. 

I took a couple of things away from this exercise:
1. I really need to freshen up on my calculus
2. Simplifying expressions throughout the process of solving a problem is important

For example, looking at a fraction like `20420505/4084101` is much harder 
to work with than the number `5`. And in most cases, `(x + 4)(x - 7)`
will be more useful than x<sup>2</sup> - 3x - 28.

Similarly, after we have finished making our code work, we should try to 
rewrite it in its simplest form.

Consider this function, which just trims a string and makes it lowercase. 
It's perfectly readable, and it _could_ be simplified a bit, but doesn't _need_ to be.

````clojure
(defn trim-and-lower [s]
  (let [trimmed (trim s)] 
    (lower-case trimmed)))
````

However, simplifying it would get rid of some noise around what's actually happening.

````clojure
(defn trim-and-lower [s]
  (lower-case (trim s)))
````

But oversimplification can sometimes be too much–or maybe it's okay.

````clojure
(def trim-and-lower (comp lower-case trim))
(defn trim-and-lower [s] (-> s lower-case trim))
````

It's really left up to you on how far to simplify a problem, 
but at what point would you say is enough? When does it become too much?

Oh, and Google says it'll take about 7.8 seconds. 