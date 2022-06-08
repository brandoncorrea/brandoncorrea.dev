---
layout: post
title:  "Linked Lists are Painful"
date:   2022-02-16 7:52:05 -0400
categories: clojure, apprenticeship
---

Some of the most buggy parts of the code I've been working in recently 
is due to a linked list structure on some entities. Duplicated nodes,
unlinked nodes, and infinitely looping chains of nodes pointing
to one another! 

Linked lists can be nasty creatures, but it doesn't _have_ to be that 
way. Surely there are ways to ensure that nodes are added, removed, and 
moved properly.

Let's brainstorm a bit... a "linked" list is just one of many 
implementations of a list. This means it could be implemented as an 
Array List, an Unordered List, or even a Hash Map. All that to say 
that this Linked List structure is just one of many interfaces to 
a "List" type. Since this is an interface, then the rest of my code
should only know that this structure is a list–it should be ignorant
of any "linked listing" logic going on.

So the solution to this problem is to isolate it–create module
for all this linked list logic, stabilize it with tons of tests,
and don't touch it! It should only know about linking and 
listing. Just as the business logic is ignorant of linked lists,
the linked list should be ignorant of the business logic.

So what would a linked list interface that knows nothing of business 
logic look like? Maybe something like this...

````clojure
(ns linked-list)

(defn move-after 
  [assoc-prev assoc-next 
   dissoc-prev dissoc-next
   prev-fn next-fn
   node target]
  (let [updated-prev  (assoc-next (prev-fn node) (next-fn node))
        updated-next  (assoc-prev (next-fn node) (prev-fn node))
        updated-node  (-> node dissoc-prev dissoc-next (assoc-prev target) (assoc-next (next-fn target)))
        updated-target (assoc-next updated-node)]
    ; Return list of changes
    (remove nil? [updated-prev updated-next updated-node updated-target])))

````

You may be thinking right now, "Who in their right mind would want to
pass in ***eight*** parameters to a function?!" Well, this function 
isn't meant to be called directly. It's meant to be used as an 
interface to hide the linked list logic.

````clojure
(ns linked-list-implementation
    (:require [linked-list :as list]
              [data :as d]))

(defn- assoc-prev [node new-prev]
  (assoc node :prev (:id new-prev)))
(defn- assoc-next [node new-next]
  (assoc node :next (:id new-next)))
(defn- dissoc-prev [node] (dissoc node :prev))
(defn- dissoc-next [node] (dissoc node :next))
(defn- prev-node [node] (d/find (:prev node)))
(defn- next-node [node] (d/find (:next node)))

(def move-after 
  (partial list/move-after 
           assoc-prev assoc-next 
           dissoc-prev dissoc-next
           prev-node next-node))
````

Now in my business logic, I can just call `move-after` from my
`linked-list-implementation`!

````clojure
(defn move-to-end [source destination]
  (list/move-after source destination))
````

If I decide later that I need another linked list implementation for a
different linked list structure, I can piece together a new partial
implementation in a different module.
