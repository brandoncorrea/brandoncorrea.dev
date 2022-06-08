---
layout: post
title:  "Drawing Hiccup"
date:   2022-02-07 7:52:05 -0400
categories: clojure, apprenticeship
---

As I work with Hiccup-like syntax, I'm realizing how easy it is to visualize what a 
component may look like on the page–at least compared to components in [Quil][quil], 
or some other "drawing" library.

I think it would be interesting to see how a Quil application might look with Hiccup
syntax in place of `(q/background 255)` or `(q/line 50 100)`.

### Dispatch Components to Draw

From a high-level view, this Hiccup/Quil rendering library would need to define 
some `draw` multimethod somewhere.

````clojure
(defmulti draw first)
````

When this multimethod encounters a Hiccup component, say `[:p "Some paragraph text"]`, 
then it would be dispatched to a `draw :p` function. `:p` would be rendered somewhere
on the page, relative to its parent component, then each child element rendered 
(just a string, in this case).

````clojure
(defmethod draw :p [[_ & children]]
  ; ... Draw p element here ...
  (doseq [child children]
    (draw child)))
````

Now we need to account for calling `draw` on the string component, which we can
probably just use the `:default` dispatch function for.

````clojure
(defmethod draw :default [content]
  ; Draw content somewhere on the page, 
  ; relative to parent element
  )
````

### Global State

So how do we know _where_ a component is on the page, who our parent elements are 
and what sort of styles should be applied to the component? The first thing that 
comes to mind is some global state of the application. Maybe a nested map following 
the component hierarchy for all the styles. 

For the parent components and positioning, there may need to be either a stack of all 
the components that have already been rendered. Another possibility might be some 
coordinates of the parent components region, which is updated as each child component
is rendered to account for the dynamic sizing of certain components like a `div`.

### Why Bother?

I don't like "drawing" elements in code. Low-level communication with a drawing library 
is definitely necessary in a lot of cases, but when all you want is something like a
single-page-app, the simplicity that the Hiccup syntax brings to browsers is 
highly desirable.

Another reason I like this is that if I wanted to convert a web application into a 
locally run program (without the web), the majority of the components would already
be built. It would just be a copy/paste and some integration work that needs to be done.

So if something like this is possible with Quil, then it's _probably_ also possible to 
do this with Java Swing or even to draw PDFs–all using just a little Hiccup!

[quil]: http://quil.info
