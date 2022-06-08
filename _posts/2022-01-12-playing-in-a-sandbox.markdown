---
layout: post
title:  "Playing in a Sandbox"
date:   2022-01-12 7:52:05 -0400
categories: clojure, apprenticeship
---

I've been working on implementing some web designs that a coworker had 
created. What's really nice though is how these designs are set up.
I'm given _only_ the HTML/CSS for the component, and I can just add 
functionality to it.

So a typical file might look something like this (usually with more content):

````clojure
(ns foo.sandbox.title)

(defn render []
  [:div.class1.class2
   [:div.container.centered
    [:h1.centered.large.title "Lorem ipsum dolor"]]])
````

This makes things so much easier since all I need to do is sort of fill in 
the blanks! So if I were to implement the component above, it would look 
something like this.

````clojure
(defn show-title [title]
  [:div.class1.class2
   [:div.container.centered 
    [:h1.centered.large.title title]]])
````

What makes this even better is that these are all routed to sandbox pages
that I can view in the browser! So when I need some guidance on how the 
component should look, I can go to the sandbox page where the component 
lives and behold it in all its glory.

This sort of structure might be common among development teams–I wouldn't
know. Maybe this structure is something you can try in your projects!
