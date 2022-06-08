---
layout: post
title:  "Drag & Drop State Management"
date:   2022-03-04 7:52:05 -0400
categories: clojure apprenticeship
---

I've been working a lot with Drag & Drop features lately, and 
it feels like the more I work on it, the more comfortable I become
with it. However, it seems that there's will always be a new problem 
to solve, as with any complex feature!

So what makes Drag & Drop complex? Well, there are three main parts to this feature:
1. Drag events when picking up elements
2. Drag-Over events when dragging an element over others
3. Drop events when dropping an element

The Drag and Drop events haven't been giving me too much trouble.
But the Drag-Over events can become complicated quickly when you 
have many elements on the page that you can "Drag-Over".

So how do we manage all these dragging and dropping events?
Here's more or less how it's currently structured:

````clojure
(def state 
  (reagent/atom 
    {:drag-item {:some :data}
     :target    {:some :more-data}}))

(defn on-hover [target]
  (swap! state assoc :target target))
(defn on-start [source]
  (swap! state assoc :drag-item source))
(defn on-drop []
  ; Do something with :drag-item and :target
  )
````

This works fine when we have one *type* of target, but what happens when we have
multiple types of items? For example, what if target becomes a keyword, versus a map?

````clojure
(on-hover {:some :target})
(on-hover {:some :other-target})
(on-hover :target-1)
(on-hover :target-2)
````

Well, now we have to handle the state differently.

````clojure
(defn on-hover [target]
  (if (keyword? target)
    (swap! state assoc :target (target-from-keyword target))
    (swap! state assoc :target target)))
````

This might be okay... but what if we need to handle those keyword targets 
differently in our `on-drop` handler?

````clojure
(defn on-drop []
  (when-let [target (:target @state)]
    (if (type-1? target)
      (do-something target)
      (do-something-else target))))
````

Now we need to keep track of this one target throughout our entire 
Drag & Drop module! By using the same state key for our targets, 
we are violating the Liskov Substitution Principle. `:target` no longer
means one thing anymore–it can mean two! If future changes follow this
pattern, then it may end up meaning even more things.

Instead, we should be using different Drag & Drop handlers for each type.

````clojure
(def state 
  (reagent/atom 
    {:drag-item     nil
     :target-type-1 nil
     :target-type-2 nil}))

(defn on-hover-type-1 [target] 
  (swap! state assoc :target-type-1 target))
(defn on-hover-type-2 [target] 
  (swap! state assoc :target-type-2 target))
(defn on-start [source]
  (swap! state assoc :drag-item source))
(defn on-drop-type-1 []
  ; Do something with :target-type-1
  )
(defn on-drop-type-2 []
  ; Do something with :target-type-2
  )
````

There are a couple different ways to go about this–multimethods may be
a better option. But the point is that we want to handle the events for
different *types* differently. This gives us more control over what happens
in each scenario. Using the same functions and keys for different scenarios 
can make things hard to track and manage.
