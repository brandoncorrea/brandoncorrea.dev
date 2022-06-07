---
layout: post
title:  "New (to me) Clojure Functions"
date:   2021-10-27 7:52:05 -0400
categories: clojure, apprenticeship
---

While working on a Tic Tac Toe game, I ran across a some neat Clojure functions:
[reduced][clojure-reduced], [repeatedly][clojure-repeatedly], and 
[get-in][clojure-get-in]!

### Short-Circuit Reduction

The `reduced` function allows you to exit a reduction early if you decide you
no longer need to iterate over a collection.

The most basic use I could think of is calling reduced on the second item 
in an infinite sequence.

````clojure
=> (reduce #(reduced %2) (range))
1
````

Here, `reduced` allows us to perform an operation on an infinite sequence, 
but only read over the first two items in the collection. This prevents us 
from entering an infinite loop with `range`.

In my Tic Tac Toe game, I used this in a `find-first` function where I needed 
only the first item matching a predicate, courtesy of the contributors on 
[StackOverflow][stack-overflow-reduced]. 

````clojure
(defn- find-first [pred coll]
  (reduce #(when (pred %2) (reduced %2)) nil coll))
````

In this example, we only need to "realize" those elements which do not satisfy 
the predicate. Once we reach a value resulting in true, we can exit and return.

### Repeating Yourself (in a good way)

The `repeatedly` function works almost the same as `repeat`, except instead of
repeating a value, it repeats a function in a lazy sequence. The resulting 
sequence is a collection of the results of sequential calls to that function.

In the example below, `repeatedly` is used to generate a list of timestamps,
each about 10 milliseconds apart.

````clojure
=> (repeatedly 5 #(do (Thread/sleep 10) (.getTime (new java.util.Date))))
(1635378115715 1635378115725 1635378115737 1635378115747 1635378115758)
````

In my Tic Tac Toe game, I use this to continually prompt the user for a move
until a valid entry is entered. This allowed me to replace the `loop`
structure I had before and reduce the amount of code for this function.

````clojure
(defn- next-move [board token]
  (find-first (partial valid-move? board)
              (repeatedly #(request-move token board))))
````

### Gettin' Default Values

The `get-in` function allows you to retrieve nested values within a map, simply by placing the 
key path in a vector.

````clojure
=> (def gerry
     {:name "Gerry"
      :address {:street "101-103 Baker Street"
                :city "Hot Coffee"
                :state "Mississippi"
                :postal-code 01134}
      :phone "248-434-5508"
      :email nil})
=> (get-in gerry [:address :city])
"Hot Coffee"
=> (get-in gerry [:favorite-color :rgba])
nil
=> (get-in gerry [:email])
nil
````

This works great for most use cases! If the key is found, then the value is returned. 
If the key is not found, we get `nil`. 

Now what if we want to check for the existence of a key while also retrieving its value? 
Both `:favorite-color` and `:email` will return `nil`, but one key exists and the other 
doesn't. How can we know about this if both keys return `nil`?

Well, `get-in` provides an optional parameter for a default value if the key is not found!
This default value can return whatever we want it to, even if it makes absolutely no sense.

````clojure
=> (get-in gerry [:email] :not-found)
nil
=> (get-in gerry [:favorite-color :rgba] :not-found)
:not-found
=> (get-in gerry [:favorite-color :rgba] "Once upon a time...")
"Once upon a time..."
````

This allows us to check for existence of a key without having going back to the map to retrieve the value.
Although, if you're not expecting any `nil` values, then the regular `get-in` form will probably work just 
fine.

[clojure-reduced]: https://clojuredocs.org/clojure.core/reduced
[clojure-repeatedly]: https://clojuredocs.org/clojure.core/repeatedly
[clojure-get-in]: https://clojuredocs.org/clojure.core/get-in
[stack-overflow-reduced]: https://stackoverflow.com/questions/10192602/return-first-item-in-a-map-list-sequence-that-satisfies-a-predicate