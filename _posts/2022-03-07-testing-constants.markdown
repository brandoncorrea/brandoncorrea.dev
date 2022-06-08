---
layout: post
title:  "Testing Constants"
date:   2022-03-07 7:52:05 -0400
categories: clojure, apprenticeship
---

A few days ago I wrote about [testing view code][testing-view-code], and 
how some things ought to be tested while others lie in this sort of grey area.
This got me thinking a little more about what we ought to test on a more 
fundamental level, rather than in a particular scenario (like view code).

So, if we were to describe or categorize all code that ought to be tested,
what would that look like? Code that satisfies A, B, and C should be tested
while code that satisfies X, Y, and Z should not be tested. After some
thought, I came to this categorization:
- If it has any moving parts, then it ought to be tested.

If this is the case, then any function which does *not* have any moving
parts should not be tested. Let's take a look at an example.

````clojure
(defn welcome []
  [:div.container 
   [:h1 "Hello, World!"]
   [:h3 "This is my website"]
   [:h4 "Favorite Foods"]
   [:ol 
    [:li "steak"]
    [:li "tacos"]
    [:li "cheeseburgers"]]])
````

This `welcome` component has **zero** moving parts–it is 100% markup (like html).

````html
<div class="container">
 <h1>Hello, World!</h1>
 <h3>This is my website</h3>
 <h4>Favorite Foods</h4>
 <ol>
  <li>steak</li>
  <li>tacos</li>
  <li>cheeseburgers</li>
 </ol>
</div>
````

Does it make sense to test this in a web application? Probably not if it
is *const*antly there. Speaking of constants, you _could_ call that `welcome`
component a constant, since its result never changes. If we categorize it as
such, then would there be any sense in testing it? Maybe...

````clojure
(def constantine "Constantine")
````

In an application where `constantine` never changes, but simply holds the name,
"Constantine", would there ever be a need to test it? At best, I could see a 
test for verifying that the value returns "Constantine". But that would be 
the _only_ test for this item.

What do you think? Should we be testing constants? If so, in what scenarios should 
we be testing them? If not, are there any scenarios you can think of where we would
need to test them?

[testing-view-code]: https://brandoncorrea.dev/clojure/apprenticeship/2022/03/03/testing-view-code.html