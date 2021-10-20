---
layout: post
title:  "Alternate Clojure Forms"
date:   2021-10-06 17:33:22 -0400
categories: clojure apprenticeship
---

As I was practicing the Bowling Game Kata today, I was experimenting with 
different ways to write the same functionality.

### Anonymous Sum

The first function I was working with was sum. The first is how the function 
was originally written and the second is how I thought to rewrite it.

I'm not quite sure how I feel about the second version of sum. On one hand, 
it's small, simple, and to the point. However, it's not as clear as the 
original function that this is indeed a function and not some other form of 
data. With the first form, the reader knows it is a function as soon as they 
see `defn-`. With the anonymous function, this is not apparent until `#(...)` 
is reached. 

Additionally, I cannot make `def` inaccessible without adding more parameters 
to the function declaration. I'd like to keep this private so that it doesn't 
conflict with other "sum" functions that the client may be using. Besides, why 
would a Bowling Game library ship a sum function?

````clojure
(defn- sum [rolls] 
  (reduce + rolls))

(def sum #(reduce + %))
````

### Destructured Strike

I really like this form of `strike?`. Right out of the function declaration, 
we see that it is only concerned with the first item in a collection. 
I can also name this first item whatever I want, like `next-roll`.

Now in the function body, I can directly compare `next-roll` to 10. I don't need 
to include additional function calls or parentheses as in the original version.

````clojure
(defn- strike? [rolls] 
  (= 10 (first rolls)))

(defn- strike? [[next-roll]] 
  (= 10 next-roll))
````

### Degenerate Score
I had originally been practicing the degenerate test of score with the `rolls`
parameter. But this isn't really necessary until the second test case. Since
the first implementation of `score` doesn't use `rolls`, we can instead use the 
ignore parameter: `_`.

The ignore parameter tells us that the function will always return some constant 
value, without having to read the function body. This isn't really significant 
in this example, since the function body is just 0. However, this can be useful 
for more complex function bodies.

````clojure
(defn score [rolls] 0)

(defn score [_] 0)
````
