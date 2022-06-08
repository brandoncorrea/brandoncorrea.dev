---
layout: post
title:  "Scrolling in ClojureScript"
date:   2022-03-08 7:52:05 -0400
categories: clojure, apprenticeship
---

I've been working a lot with features involving scrolling after clicking 
a button, which involves some JavaScript to invoke the scrolling behavior.

This was a bit unnerving at first, since I hadn't really done any scrolling 
features in the past, whether with ClojureScript or plain old JavaScript.
However, it turns out that scrolling in ClojureScript isn't as hard as I thought!

Using JavaScript's [scrollTo][scrollTo] interface, we can pass in a map of scroll 
options where we can specify how far to scroll and the scrolling behavior.

````clojure
(defn scroll-down [node distance]
  (.scrollTo node (clj->js {:top distance})))
(defn scroll-right [node distance]
  (.scrollTo node (clj->js {:left distance})))
````

The `:top` and `:left` keys act as the [x, y] coordinates of the top-left
corner of the scroll container. So if we provide `{:top 100 :left 50}`, 
then the scroll container's top-left corner will appear 100 pixels from
the top and 50 pixels from the left of the content within `node` 
(which should be the scroll container).

Since we didn't provide any `:behavior` to the function before, the browser
will decide whether to "jump" to the specified position instantly or scroll
smoothly to the destination. To override the browser defaults, provide
either `"instant"` or `"smooth"` to the `:behavior` option.

````clojure
(defn scroll-down-smoothly [node distance]
  (.scrollTo node (clj->js {:behavior "smooth" :top distance})))
(defn scroll-down-instantaneously [node distance]
  (.scrollTo node (clj->js {:behavior "instant" :top distance})))
````

Now, there may be a case where you need a "scroll to end" function. How do
we know how far to scroll? This would just be whatever the height or width of
the scroll container is!

````clojure
(defn scroll-to-bottom [node]
  (.scrollTo node (clj->js {:top (.-scrollHeight node)})))
(defn scroll-to-end [node]
  (.scrollTo node (clj->js {:left (.-scrollWidth node)})))
````

The `:top`, `:left`, and `:behavior` options open you up to tons of different
ways of adding scroll functionality in your application! This is just the surface
and there's probably even more I have yet to uncover.

[scrollTo]: https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollTo
