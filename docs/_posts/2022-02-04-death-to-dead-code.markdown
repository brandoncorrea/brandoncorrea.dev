---
layout: post
title:  "Death to Dead Code"
date:   2022-02-04 7:52:05 -0400
categories: apprenticeship
---

All the code we write has some cost associated with it. It
will almost always change at some point in the product's 
lifetime, so we should do our best to keep it clean. 

One way we can do this is by removing any unused code from 
our codebase. But what is dead code? How can we identify it 
and how can we eliminate it?

### Unreferenced Code

Let's say we have a project that prints some arithmetic.
We originally started by using a `times-two` function, but later changed
this to `plus-ten` and left `times-two` in our code. This function 
now serves no purpose–it is dead and should be deleted.

````clojure
(defn times-two [x] (* x 2))
(defn plus-ten [x] (+ x 10))
(defn -main []
  (println "5 + 10 = " (times-two 5)))
````

Some functions must comply to an interface, and may not use one or more
of their parameters–I'm not talking about those functions in this next example.

At some point, `make-balloon` used all three parameters: `shape`, `gas`,
and `fastener`. But at some point it was decided that `shape` was no longer
necessary for the implementation. This resulted in shape to no longer be
referenced within the function.

`shape` should be removed from the parameter list and references to this function
should be updated. This will consequently make dependent modules easier to maintain,
as this function now requires fewer parameters.

````clojure
(defn make-balloon [shape gas fastener]
  #_...)
````

Unreferenced code is useless. It serves no purpose in our codebase 
and distracts us from the main idea of the module. It makes refactoring harder–
maybe this code references another function that you need to refactor, but 
your refactoring causes this function to fail in some way. Now you're stuck
managing this dead code.

One of the more frightening results of unreferenced code is that it can wind up in a 
chain of dead code. A function may have a hundred references, but each 
of those references winds up in a dead-end at some unused code. So while this function
may have one hundred references, it is essentially dead.

### Commented out Code

````clojure
;(defn times-two [x] (* x 2))
(defn plus-ten [x] (+ x 10))
(defn -main []
  ;(println "5 x 2 = " (times-two 5))
  (println "5 + 10 = " (times-two 5)))
````

While this is slightly better than leaving in unreferenced code,
as the compiler will ignore it, this still should not be done.
Again, this just clutters our functions and modules and distracts the 
reader from what's happening.

Now maybe this is a function you _might_ end up using somewhere down the line.
It has plenty of tests and took a long time to write. Well, if you're using
some sort of version control system, then this code should still exist in its history.

Don't get stuck managing code that you _might_ need later. Odds are it will never be used,
or it will become so out-of-date that you'd be better off rewriting it anyways.
