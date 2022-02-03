---
layout: post
title:  "Testing Hard-to-Reach Places"
date:   2022-02-0 7:52:05 -0400
categories: clojure apprenticeship
---

Lately I've been tempted to just make my code work without
properly testing it, especially when it comes to 
ClojureScript/JavaScript interop. Certain things are just 
hard or painful to test, but if you find yourself in this
situation, you _must_ resist the urge to code without tests!

Let's take a look at a component that can be painful to test.
We have a JavaScript function that we need to call, but for 
whatever reason, we're unable to validate it from our test code.

````clojure
(defn submit-form [product event]
  (do-something product)
  (.-someUntestableFunction event))

(defn submit-button []
  [:div
   [:button#-submit-button
    {:on-click (partial submit-form product)}]])
````

What do we do when we have a function that we can't reach?
Maybe it throws an exception in our test, or maybe there's 
just no way to verify a change in the document.

If we know this is a stable function, and we just need to
verify that it is invoked, then we can just `stub` it out
and verify it that way.

First thing we'd need to do is put it behind a clojure function.

````clojure
(defn some-untestable-function [event]
  (.-someUntestableFunction event))
(defn submit-form [product event]
  (do-something product)
  (some-untestable-function event))

(defn submit-button []
  [:div
   [:button#-submit-button
    {:on-click (partial submit-form product)}]])
````

Now we can use [speclj][speclj] to `stub` out this function and
verify that it is executed when we click our submit button!

````clojure
(it "tests some untestable action"
  (with-redefs [some-untestable-function (stub :a-testable-function!)]
    (click! "#-submit-button")
    (should-have-invoked :a-testable-function!)
    #_some_other_validations...))
````

This allows us to reach code that would otherwise be unreachable
and test functions that fail because of some interop error.

This sort of stubbing out of functions also gives us better
separation of concerns. We know that a function is stable
and tested–we just need to make sure that it is invoked
when another part of the code is executed.

[speclj]: https://github.com/slagyr/speclj