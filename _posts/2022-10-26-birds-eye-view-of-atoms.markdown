---
layout: post
title:  "Bird's-Eye View of Atoms"
date:   2022-10-26 7:52:05 -0400
categories: clojure
---

## Atoms

Atoms hold state in memory that can persist outside the scope of a function.

````clojure
=> (def state (atom 10))
=> @state
10
````

They can be changed to another value.

````clojure
=> (reset! state 5)
5
=> @state
5
````

Or updated with a function.

````clojure
=> (swap! state 5 inc)
6
=> @state
6
=> (swap! state - 4)
2
=> @state
2
````

`@` is shorthand for `deref` – they are synonymous.

````clojure
=> (deref state)
2
=> @state
2
````

`state` refers to the `Atom` object while `@state` refers to the value it currently holds.

````clojure
=> state
#object[clojure.lang.Atom 0x167f2f68 {:status :ready, :val 2}]
=> @state
2
````

## Reagent Atoms

Reagent atoms work the same as regular atoms.

````clojure
=> (def state (reagent/atom 10))
=> (reset! state 5)
5
=> (swap! state inc)
6
=> @state
6
````

These, however, have an additional `track` function. `track` only requires a 
0-arity function, which will be invoked when dereferenced.

````clojure
=> (def state (reagent/atom {:x 1 :y "Hello" :z :world}))
=> (def x (reagent/track #(:x @state)))
=> (def y (reagent/track #(subs (:y @state) 2)))
=> (def z (reagent/track (fn [] (:z @state))))
=> @x
1
=> @y
"llo"
=> @z
:world
````

A `track` can only be derefenced. It is a readonly variable that cannot be updated directly.
However, updating `state` will cause the `track` to update with it.

````clojure
=> (swap! state assoc :y "Goodbye")
=> @y
"odbye"
````
