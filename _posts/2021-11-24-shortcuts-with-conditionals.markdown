---
layout: post
title:  "Shortcuts with Conditionals"
date:   2021-11-24 7:52:05 -0400
categories: clojure, apprenticeship
---

### Conditional Operators

You're probably familiar with `and` and `or` in Clojure. If not, these are
basically the same as the `&&` and `||` operators in any other 
language–unless you're using VB (I'm sorry).

So in Clojure, I can do something like this and the operator will work 
the same as you'd expect in any other language.

````clojure
user=> (and true true true false true)
false
user=> (or false false false true false)
true
````

### Everything is a Boolean

One cool thing about Clojure is that just about everything evaluates to 
a boolean! So I could modify the conditionals like so...

````clojure
user=> (and [1 2 3] "a" :b \C (fn [a b] (+ a b)) {} 5.2)
5.2
user=> (or [1 2 3] "a" :b \C (fn [a b] (+ a b)) {} 5.2)
[1 2 3]
````

Why does Clojure do this? Well, I couldn't tell you WHY they decided to 
make it work this way, but in the documentation for `and` and `or`:
- `and` returns the last truthy value or the first falsey value
- `or` returns the first truthy value or the last falsey value

Since everything in the `and` form evaluates to true, then we just got back 
the last value in the form. Similarly in the `or` form, since the first item
evaluates to true, we received that value back.

### Refactoring Conditionals

Why does it matter that we get values back from conditional operators?
Well, coming from an OO background I'm used to writing stuff like this:

````java
if (a != null)
    return a;
else if (b != null)
    return b;
else if ...
````

So when I need to write a similar function in Clojure, I'm immediately 
thinking of structures like this:

````clojure
(defn do-something [a b c]
  (cond 
    a a
    b b
    c c
    :else nil))
````

This looks silly. We're literally returning one of the values which evaluate
to true. Then if every value results in false, we just return `nil` anyways!

Now this is a function I absolutely need in my application–everything will
fall apart otherwise. How can this be improved? 

Recall that `or` will return the first truthy value or the last falsey value.
Well, `do-something` looks a lot like it's trying to mimic that sort of logic.
Let's refactor it to use `or` instead and see what we get.

````clojure
(defn do-something [a b c]
  (or a b c nil))
````

Well hold on now–`do-something` isn't actually _doing_ anything! It's just
calling or on the same operators (plus `nil`) it's given! We may as well
just remove `do-something` altogether and put its body in the parent function.

````clojure
(defn parent [a b c]
  ; ...
  (or a b c nil))
````
