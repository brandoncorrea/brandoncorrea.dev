---
layout: post
title:  "XOR Gate in Clojure"
date:   2022-02-14 7:52:05 -0400
categories: clojure, apprenticeship
---

### Logic Gates

Some of the most basic logic gates in computing are 
AND, OR, and NOT. From these, we can compose other logic 
gates such as NAND (NOT AND) and NOR (NOT OR). Now there's
one more (less frequently used) logic gate: XOR (exclusive or).

The XOR gate will open when there is exactly one truthy 
condition. If there is more than one truthy conditions,
or no truthy conditions, then the gate will close.

In Clojure, we have the `and`, `or` and `not` functions,
which correspond to AND, OR and NOT, but we don't have an
`xor` function. After some thought on this, I realized its 
implementation would raise a couple issues.

### The XOR Problem

`and` and `or` are designed to return _values_–booleans are
not guaranteed. So the first question we need to ask in 
an XOR implementation is "What should `xor` return?" 
- If the result is true, then return the truthy value
- If there are no truthy values, return the last falsy value
- If is are more than 1 truthy value, return... which one?

We can't return the "first" or "second" truthy value 
when there are multiple truthy conditions, because then 
the code using this function would see the XOR result as 
true, which would be a lie.

We _could_ have `xor` return `false` when there are two truthy
values, but this would be inconsistent with the falsy result
we'd get when there are _no_ truthy values.

One way we could solve this is to redefine the results of XOR:
- If the result is true, then return the truthy value
- If the result is false, return `false`

While this is still inconsistent with the results we'd see with 
`and` and `or`, the function results would at least be consistent 
with itself.

### XOR Implementation

Now that we have requirements we can follow, let's implement
`xor`!

We want to start with testing zero parameters. After checking 
`and` and `or` in the REPL (with no parameters), these
return `true` and `nil`, respectively. So I think we have a
little freedom on how we define are default xor value. 

Since `xor` is a variation of `or`, I'll implement this as
a falsy result.

````clojure
(describe "Exclusive Or"
  (it "is false when no conditions are provided"
    (should= false (xor))))
````

This fails to compile, so now we can create our function.

````clojure
(defn xor [] false)
````

Pass! Now to create another failing test. Since our function
will always return `false`, we should write a test that expects
`true`.

````clojure
(it "is true when the only condition is 'true'"
  (should= true (xor true)))
````

This fails to compile, because our function is arity-0. 
We can solve this by using a rest parameter: `&`. Also,
to keep our first test from failing, we will need to wrap 
this in an `or` to return `false` when the first condition
is falsy.

````clojure
(defn xor [& conditions]
  (or (first conditions) false))
````

In order to achieve the "exclusive" part of XOR, we need to 
make sure that we only get one truthy value. So let's test
two truthy conditions with `xor`.

````clojure
(it "is false when two truthy conditions are provided"
  (should= false (xor 1 2)))
````

Here's where we start to see the XOR logic come in. 
We'll destructure our function and take out the first two
values. If both are true, then we'll return `false`. 
Otherwise, we'll return the first truthy. If neither 
condition results in truthy, then we'll default to `false`

````clojure
(defn xor [& [a b]]
  (if (and a b)
    false
    (or a b false)))
````

Now this function should really work with more than two
arguments. Let's test three...

````clojure
(it "evaluates three conditions"
  (should= :truthy (xor false false :truthy)))
````

If we just add the third parameter to `xor`, we can
achieve arity-3 functionality.

````clojure
(defn xor [& [a b c]]
  (if (and a b)
    false
    (or a b c false)))
````

Now this is starting to look a little funny... we only
see `a` and `b` in the `if` condition, but `a`, `b` and `c`
should all be used in determining the result. Also, while 
our function works with three arguments, what about four?
Or four-hundred?? It's time to refactor!

````clojure
(defn xor [& conditions]
  (reduce
    (fn [a b] (if (and a b) false (or a b)))
    false
    conditions))
````

Great! Now `xor` will work as an N-arity function. But
we can make this better... let's define that reducing 
function separately.

````clojure
(defn- exclusive [prev condition]
  (if (and prev condition)
    false
    (or prev condition)))

(defn xor [& conditions]
  (reduce exclusive false conditions))
````

Much better! Now as-is, our function may not behave as 
expected when given a `nil` argument. Let's test that.

````clojure
(it "is false when given a nil argument"
  (should= false (xor nil)))
````

Hmm... looks like we are getting `nil` back. To fix this,
we can just swap the two variables in the last line of our 
`exclusive` function. Since we are defaulting `reduce` with
`false`, prev will always be either a truthy value or `false`.

````clojure
(defn- exclusive [prev condition]
  (if (and prev condition)
    false
    (or condition prev)))

(defn xor [& conditions]
  (reduce exclusive false conditions))
````

Lastly, I'd like `xor` to behave similarly to `or` and `and` 
as far as lazy-evaluation. For example, I can pass a 
divide-by-zero condition to either of these functions and
neither will throw an error because of the shortcut logic
preventing the divide-by-zero condition from being evaluated.

````clojure
=> (or true (/ 3 0))
true
=> (and false (/ 3 0))
false
````

Let's create a test for this.

````clojure
(it "does not evaluate remaining conditions when two truthy values are found"
  (should= false (xor true true (/ 3 0))))
````

As expected, we see a divide-by-zero error in the console.
Now let's make this pass, we'll need to do two things:
- Make `xor` a macro using `defmacro` to prevent parameter evaluation
- Add `reduced` to the `exclusive` function to "shortcut" out of the
`reduce` function, preventing subsequent conditions from being evaluated.

That's it! This function may not _look_ like a macro, 
it satisfies my lazy-evaluation requirement. While there
may be more ways to improve this function, I'm happy with 
this implementation.

### Source

````clojure
(ns exclusive-or.core)

(defn- exclusive [prev condition]
  (if (and prev condition)
    (reduced false)
    (or condition prev)))

(defmacro xor [& conditions]
  (reduce exclusive false conditions))
````

### Spec

````clojure
(ns exclusive-or.core-spec
  (:require [speclj.core :refer :all]
            [exclusive-or.core :refer :all]))

(describe "Exclusive Or"
  (it "is false when no conditions are given"
    (should= false (xor)))
  (it "is true when the only condition is 'true'"
    (should= true (xor true)))
  (it "is false when there are two truthy values"
    (should= false (xor 1 2)))
  (it "evaluates three conditions to truthy"
    (should= :truthy (xor false false :truthy)))
  (it "is false when given a nil argument"
    (should= false (xor nil)))
  (it "does not evaluate remaining conditions when two truthy values are found"
    (should= false (xor true true (/ 3 0)))))
````
