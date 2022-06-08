---
layout: post
title:  "Multimethods Are Just Interfaces"
date:   2022-03-15 7:52:05 -0400
categories: clojure, apprenticeship
---

I'm currently working on some drag & drop updates, and I've been
moving some things over to use multimethods rather than several
named functions. I personally like this approach because I don't 
have to think of a ton of different names for the same type of 
behavior. Rather than having something like  `drag-over-this`, 
`drag-over-that`, or `drag-over-abc`, I can just have one `drag-over`
function that dispatches to `:this`, `:that`, and `:abc`. 

Something I noticed while adding in these multimethods is that
I could group the functions by a single dispatch value, and that 
these groupings looked quite similar.

````clojure
(defmethod on-drop :foo [])
(defmethod drag-over :foo [])
(defmethod drag-out :foo [])

(defmethod on-drop :bar [])
(defmethod drag-over :bar [])
(defmethod drag-out :bar [])

(defmethod drag-start :abc [])
(defmethod drag-end :abc [])

(defmethod drag-start :123 [])
(defmethod drag-end :123 [])
````

One way of looking at these groups of functions is as an implementation
of two different interfaces. In C#, I might call these `IDraggable` and
`IDroppable`, where `IDraggable` is something that can be dragged around
and `IDroppable` is something that can be dropped on. 

In additional to these two interfaces, we can have types implement them 
**both**!

````clojure
(defmethod on-drop :xyz [])
(defmethod drag-over :xyz [])
(defmethod drag-out :xyz [])
(defmethod drag-start :xyz [])
(defmethod drag-end :xyz [])
````

By distinguishing these as separate interfaces, we can make certain assumptions 
about how they will be used. For example, you can drag an `:abc` item onto a 
`:foo` item, but you cannot drag it onto a `:123` item. Therefore, we don't need
to handle the case where `:abc` is dropped onto `:123`.

Similarly, both `:foo` and `:bar` are unable to be dragged, so we do not need
to handle drag start or end cases for either of those types.

At the end of the day, an interface is just a facade that promises some concrete
implementation, and this is it in Clojure.
