---
layout: post
title:  "Forcing Components to Render in Reagent"
date:   2022-03-23 7:52:05 -0400
categories: clojure apprenticeship
---

I ran into an issue with Reagent the other day where some 
if my components weren't rendering properly on update.
Here's the general component structure I was working with.

````clojure
(defn user-info [user]
  [:div
   [:h5 (:name user)]
   [:img {:src (:avatar user)}]])

(defn home []
  [:div
   [:h1 "Hello, world!"]
   [user-info (current-user)]])
````

What ended up fixing this was just changing the way `user-info`
was being called in `home`.

````clojure
(defn home []
  [:div
   [:h1 "Hello, world!"]
   (user-info (current-user))])
````

By using the square brackets `[]`, `user-info` was being rendered
as its own component. However, by calling it as a function instead
using parentheses, it now becomes part of the `home` component. 
This means that each time `home` is re-rendered, `user-info`
is re-rendered along with it.

I was a bit confused at first why this worked over calling it as
a component. My guess is that the change to `user-info` was so 
small that Reagent didn't think to re-render it.

In any case, exchanging the square brackets for parentheses fixed
my issue. So if you're running into the same issue, try doing that
if you don't absolutely _need_ your component to be rendered as
its own component.
