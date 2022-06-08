---
layout: post
title:  "Composing Small Namespaces"
date:   2022-01-21 7:52:05 -0400
categories: clojure, apprenticeship
---

Constantly refactoring after each passing test is an important
practice for maintaining a clean codebase. Even after you finish
implementing a feature, there's usually some last minute cleanup 
that has to happen.

This week I was implementing a new feature and, after completing it, 
realized I had several hundred lines in a single file!
This needed to be broken down a bit... but how?

At the beginning of chapter 10 in his book, [Clean Code][clean-code], 
Uncle Bob writes:

    The first rule of classes is that they should be small.
    ...
    With functions we measure size by counting physical lines. With 
    classes we use a different measure. We count responsibilities.

While Clojure doesn't have classes, we can still apply this rule to
namespaces.

### Grouping Features

In a large file like the one I had, you should notice some groups of 
functions belonging to the same item. Maybe they're scattered throughout 
the namespace, but they all still terminate at a single function. This
is a good place to start pulling out responsibilities into new namespaces.

### Refactoring

The code I was working with looked something like this:

````clojure
(ns app.settings
  (:require #_...))

(defn title [state] [:h1 (:title state)])

(defn editable-description [state editing?] #_...)
(defn uneditable-description [state editing?] #_...)

(defn description [state]
  (let [editing? (reagent/atom false)]
    (fn []
      (if @editing?
        [editable-description state editing?]
        [uneditable-description state editing?]))))

; more helper functions for users

(defn list-users [state view] #_...)
(defn create-users [state view] #_...)
(defn edit-users [state view] #_...)

(defn users [state]
  (let [view (reagent/atom :list)]
    (fn []
      (cond (= :list @view)   [list-users state view]
            (= :create @view) [create-users state view]
            (= :edit @view)   [edit-users state view]))))

(defn page [state]
  [:div#page-container.container
   [:ul#menu-items
    [:li [title state]]
    [:li [description state]]
    [:li [users state]]]])
````

This namespace has three clear responsibilities: title, description, and
users. This doesn't necessarily mean that we need three new namespaces. 
However, it also doesn't mean that one of these responsibilities won't
need more than one namespace.

Before refactoring, I'd like to move this file into a "settings" folder.
This way my namespaces will look like this:
- `app.settings.page`
- `app.settings.title`
- `app.settings.description`
- `app.settings.users`

### Starting with Behemoths

I personally prefer moving larger groups of code out before attacking 
the smaller things. This just helps make smaller groups of responsibility
more easily identifiable. Though, it may not always be possible to start
off with the larger things.

````clojure
(ns app.settings.users
  (:require #_...))

; more helper functions for users

(defn list-users [state view] #_...)
(defn create-users [state view] #_...)
(defn edit-users [state view] #_...)

(defn users [state]
  (let [view (reagent/atom :list)]
    (fn []
      (cond (= :list @view)   [list-users state view]
            (= :create @view) [create-users state view]
            (= :edit @view)   [edit-users state view]))))
````

````clojure
(ns app.settings.page
  (:require [app.settings.users :as u]
            #_...))

(defn title [state] [:h1 (:title state)])

(defn editable-description [state editing?] #_...)
(defn uneditable-description [state editing?] #_...)

(defn description [state]
  (let [editing? (reagent/atom false)]
    (fn []
      (if @editing?
        [editable-description state editing?]
        [uneditable-description state editing?]))))

(defn page [state]
  [:div#page-container.container
   [:ul#menu-items
    [:li [title state]]
    [:li [description state]]
    [:li [u/users state]]]])
````

Already our `page` component is looking much cleaner! Now that we've
integrated `users` and all our tests are passing, we will want to move
our tests out into their own namespace as well. `users` is now responsible
for itself, so it must have its own suite of tests.

At this point, it's safe to start refactoring `users` the same way
we refactored `settings`. Can anything other groups of responsibility
be pulled out? Can any of our functions names be more descriptive?

### Conclusion

When you refactor your namespaces in to smaller groups of namespaces, 
your source and test code becomes easier to read. Looking for functions 
and responsibilities doesn't turn into scrolling through code and doing
text searches.

When working in teams, you could have three people working on the 
settings page: title, description and users. When they all merge, 
conflicts between branches should be few to none!

[clean-code]: https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882/ref=sr_1_1?gclid=CjwKCAiA866PBhAYEiwANkIneHPlvWbqWe2dBblUMtMKrGQmGxoSP_-_q_d2latlcfgu7jaVwBnn4RoCjOQQAvD_BwE&hvadid=490225795116&hvdev=c&hvlocphy=9012076&hvnetw=g&hvqmt=e&hvrand=7758294580785243719&hvtargid=kwd-4606836158&hydadcr=24665_13446940&keywords=clean+code&qid=1642857455&sr=8-1