---
layout: post
title:  "Thinking Backwards"
date:   2021-10-25 7:52:05 -0400
categories: clojure apprenticeship
---

A few days ago I discussed a recursive algorithm for calculating the number of 
combinations given any set of values. After implementing this in Clojure, this 
resulted in some clunky code.

### The Recursive Version

This got my Euler problem working, but there's a lot going on here:
- The comment at the top that nobody will want to read
- The vague function and variable names
- The if statement at the top (why is that there...?)

````clojure
; combo-count implements a generic function to find the
; number of unique combinations given any set of values,
; where N represents the number that a given value repeats itself:
; F(Nn...Nm) = Nn(F(Nn+1...Nm) + 1) + F(Nn+1...Nm)
(defn combo-count [group-counts]
  (if (empty? group-counts)
    0
    ; rest-count represents F(Nn+1...Nm)
    (let [rest-count (combo-count (rest group-counts))]
      (+ (* (first group-counts)
            (inc rest-count))
      rest-count))))
````

### The Loop Version

Almost every recursive function can be implemented as a loop, but how am I going 
to do that? I need the last values of the recursion in order to determine the 
end result, right?

Well, it turns out that order doesn't exactly matter with this function. So 
rather than starting from the end, we can start from the top and work backwards 
instead. 

To do this, we would take the formula as if it had only two parameters and pass 
in the next group count and the accumulated total.

````clojure
(defn- combos-of-two [A B]
  (+ (* A (inc B)) B))

(defn count-combinations [values]
  (loop [total 0
         [next & rest-frequencies] (map second (frequencies values))]
    (if next
      (recur (combos-of-two next total) rest-frequencies)
      total)))
````

This version is much better than the recursive form. We have a more 
descriptive name, and we can accept a collection of `values` instead of
a collection of "group-counts".

### The Reduce Version

While this version works fine, it can still be made better. All this 
loop is doing is performing an operation on each item in a list and returning 
some accumulated value. This happens to be something that `reduce` is great at!

What we want to do here is `reduce` that `combos-of-two` function over the 
collection of frequencies. We'll also have to update the combos function to 
handle the case when `values` is empty.

```clojure
(defn- combos-of-two
  ([] 0)
  ([A B] (+ (* A (inc B)) B)))

(defn count-combinations [values]
  (reduce combos-of-two (map second (frequencies values))))
````

And that should be it! Using `reduce`, we are able to get leave out just about 
every unnecessary detail in our function; we are left with just the core
`combos-of-two` function and the `frequencies` of each value.