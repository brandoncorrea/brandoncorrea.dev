---
layout: post
title:  "Doing Less in Lower Levels"
date:   2022-03-01 7:52:05 -0400
categories: clojure, apprenticeship
---

When working with functions at lower level in a codebase,
it's important to keep in mind who will be using those functions 
and what they may be expecting from them. 

Consider this function that updates some time attributes in a map.

````clojure
(defn set-hour [time hour]
  (assoc time :hour hour :minute 0 :second 0))

(defn noon [clock]
  (set-hour (:time clock) 12))
````

The name of this function is `set-hour`, and not only does it set the
`:hour`, but it also sets `:minute` and `:second` to 0! Surely someone
else who might use this function would be surprised to see other values 
in their map being changed.

There are a couple ways we can fix this:

1. Move the `:minute` and `:second` changes to a higher level.

````clojure
(defn set-hour [time hour]
  (assoc time :hour hour))

(defn noon [clock]
  (-> (:time clock)
      (set-hour 12)
      (assoc :minute 0 :second 0)))
````

2. Rename the function to something more suitable to its behavior.

````clojure
(defn bottom-of-hour [time hour]
  (assoc time :hour hour :minute 0 :second 0))

(defn noon [clock]
  (bottom-of-hour (:time clock) 12))
````

While it probably doesn't make much sense to extract functionality in this case,
the idea is that our functions don't do anything more than what we'd expect of them.
Either of these solutions will make your code clearer and more readable for 
the next person working in the project. 
