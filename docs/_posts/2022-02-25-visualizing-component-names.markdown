---
layout: post
title:  "Visualizing Component Names"
date:   2022-02-25 7:52:05 -0400
categories: clojure apprenticeship
---

When writing view code, careful selection of component names 
is just as important as selecting function or variable 
names. When looking at these functions put together, they
should give you a general idea of what the page *looks* like,
or at least how it's structured.

I was working on an application that needs to render story cards
in a column view, but there are many columns each containing
different kinds of stories. The view code looked something like this...

````clojure
(defn archive [stories]
  [:div.column
   #_...content...])

(defn backlog [stories]
  [:div.column
   #_...content...])

(defn iteration-container [iteration]
  [:div.column
   #_...content...])

(defn home-page []
  [:div
   [archive archive-stories]
   [backlog backlog-stories]
   (for [iteration iterations]
    [iteration-container (iteration-stories iteration)])])
````

This works fine functionally, but what about readability? Would someone
new on the project know that each of these are actually structured the same,
without looking at the `archive`, `backlog`, and `iteration-container` 
components?

What if we had something more like this?

````clojure
(defn home-page []
  [:div
   [archive-column archive-stories]
   [backlog-column backlog-stories]
   (for [iteration iterations]
    [iteration-column (iteration-stories iteration)])])
````

Without looking at any of the lower-level components, this tells me
that the home page is made up of a bunch of columns! I can visualize
this in my head–I don't have to decipher what each of these components 
mean. What is desired is to have components that are very telling of 
what they are.

[apple]: https://www.apple.com/