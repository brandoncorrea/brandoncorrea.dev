---
layout: post
title:  "Tests That Read"
date:   2021-12-08 7:52:05 -0400
categories: clojure apprenticeship
---

When writing my tests, I only ever considered how it read in the test file,
but never thought about how it would read in the test results from the console.
Yesterday I [wrote about][wator-kata] how I would perform the Wa-Tor Kata – 
so I'll be using that for the examples below. 

Normally in my specs, I would write tests like this:

````clojure
; Spec
(describe "Wa-Tor Kata"
  (describe "move"
    (it "Results in unoccupied neighbor when available"
      (should= [0 1] (move {[0 0] fish [0 1] nil} [0 0])))))

; Console Output 
Wa-Tor Kata move Results in unoccupied neighbor when available
````

When I'm testing a function (`move` in this case), I typically use the function 
name in the `describe` text. This isn't always a bad thing, but can be in some cases.

In this case, I'm describing `move`, but sharks "move" too. If I decided to add another
creature in the Wa-Tor simulation, they might also move. So since we're specifically 
testing how _fish_ move, then we are _describing_ how _fish move_

````clojure
; Spec
(describe "fish move" #_...)

; Console Output 
Wa-Tor Kata fish move Results in unoccupied neighbor when available
````

This is better, but could still use improvement. We have some capitalization in the 
middle of a sentence, "Results". We could fix that, but how can we get this sentence to 
really flow while delivering the same idea?

We could ask ourselves, what do fish do when they move? If you were answering this question
to another person, you might say "Fish move to an unoccupied neighbor when one is available".
So let's just use that in our spec.

````clojure
; Spec
(it "to an unoccupied neighbor when one is available"
  (should= [0 1] (move {[0 0] fish [0 1] nil} [0 0])))

; Console Output 
Wa-Tor Kata fish move to an unoccupied neighbor when one is available
````

This reads much more like a human than a robot. 
Another more obvious case is the `neighbors?` tests.

````clojure
; Spec
(describe "neighbors?"
  (it "[0 0] and [0 0] are not neighbors"
    (should= false (neighbors? [0 0] [0 0]))))

; Console
Wa-Tor Kata neighbors? [0 0] and [0 0] are not neighbors
````

The structure of this sentence makes no sense and is sure to confuse someone trying to 
understand it. So what are we actually describing here? 

The function takes two positions and answers the question: Are these two positions neighbors?
The subjects we are describing are positions, so let's `describe "positions"`. We will see
that this produces a nice description that makes much more sense.

````clojure
; Spec
(describe "positions" #_...)
; Console
Wa-Tor Kata positions [0 0] and [0 0] are not neighbors
````

If your non-programmer friends were trying to understand how fish move
in the Wa-Tor simulation solely based off your tests, what would make the most sense to them?
- Wa-Tor Kata move Results in unoccupied neighbor when available
- Wa-Tor Kata fish move to an unoccupied neighbor when one is available

What about how positions relate to each other?
- Wa-Tor Kata neighbors? [0 0] and [0 0] are not neighbors
- Wa-Tor Kata positions [0 0] and [0 0] are not neighbors

Our specs should describe the application's behavior – not the functions we are testing.

[wator-kata]: https://brandoncorrea.github.io/clojure/apprenticeship/2021/12/07/wator-kata-moving-fish.html