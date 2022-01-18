---
layout: post
title:  "Skip to the Main Idea"
date:   2022-01-17 7:52:05 -0400
categories: clojure apprenticeship
---

Sometimes you run into some functionality that you want to implement,
but it's just a _little_ bit out of reach. Maybe it's something behind an
API, database, or a larger function that you don't necessarily care about.

Today I ran into a similar issue–I wanted to add functionality to an HTML
renderer, but didn't want to create my tests around the actual HTML rendering
function. I knew if I did that, I end up having DOM calls in every test 
to make sure my function did what it was supposed to.

Let's say I have this function and I want to change the way the tag name
(`createDom`'s first parameter) is selected.

````clojure
(defn hiccup->html [hiccup]
  (createDom (subs (first (str hiccup)) 1) (clj->js (second hiccup))))
````

This function depends on `createDom`, which is in some HTML DOM library 
and is quite a bit of trouble to set up. In addition to setup, I'll need to
select the tag name from the return value for each test! Well that's a lot
of work, and I'm _really_ lazy.

One way we can get around this is to create a new function, `tag-name`,
and test that instead!

````clojure
(defn tag-name [hiccup] 
  )
(defn hiccup->html [hiccup]
  (createDom (subs (first (str hiccup)) 1) (clj->js (second hiccup))))
````

Before we start using `tag-name` in our higher level function, we want to
at _least_ implement the existing functionality so that
our higher level function does not break.

````clojure
; spec
(describe "tag-name"
  (it "is parsed as an h1"
    (should= "h1" (tag-name [:h1])))
  (it "is parsed as a div"
    (should= "div" (tag-name [:div]))))
; src
(defn tag-name [hiccup]
  (subs (first (str hiccup)) 1))
(defn hiccup->html [hiccup]
  (createDom (subs (first hiccup) 1) (clj->js (second hiccup))))
````

At this point, we can refactor `hiccup->html` to use `tag-name`, and 
continue to test new features under the `tag-name` function.

````clojure
(defn tag-name [hiccup]
  (subs (first (str hiccup)) 1))
(defn hiccup->html [hiccup]
  (createDom (tag-name hiccup) (clj->js (second hiccup))))
````

This keeps our tests focused and decluttered from irrelevant information.
After all, we only care about how tag names are selected–not how the DOM is 
rendered. Although it wouldn't be a bad idea to make sure tests already exist
around the higher-level function.
