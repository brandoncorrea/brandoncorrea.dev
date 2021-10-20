---
layout: post
title:  "Mutlimethods"
date:   2021-10-07 7:52:05 -0400
categories: clojure apprenticeship
---

Today I read about multimethods in Clojure, what they are and how they're used. 
Mutlimethods essentially allow you to create _multiple_ functions with the same
name that perform different operations based on the data it receives. So say
you have a function, `add`, and you want it to operate differently between 
collections, strings, and numbers, multimethods would be one way to do that.

Coming from a background of strongly typed languages, multimethods seem like 
they'd also be useful when trying to filter out bad data types. Although, this 
hasn't really been an issue for me yet in Clojure. 

### Implementation

First, we want to create our dispatching function. This basically returns some
identifier that maps to one of the multimethods we will define later.

It's good to be aware that this may return any value, including `nil`, and it 
will work the same as any other value returned! So if we defined a multimethod 
for `nil`, then `move` would dispatch to that method.

````clojure
(defmulti move (fn [animal] (:kind animal)))
````

Next, we want to define our multimethods for `move`. Each function starts with
`defmethod` and the name of the multimethod. The third parameter is they key
that is returned by the dispatching function above, the `:kind` of animal. The
rest of the multimethod should read just like any other Clojure function.

````clojure
(defmethod move :bird 
  [animal]
  (str (:name animal) " flies into the sunset!"))

(defmethod move :fish 
  [animal] 
  (str (:name animal) " swims through the ocean blue!"))

(defmethod move :rodent
  [animal]
  (str (:name animal) " scurries through the grassy field!"))

(defmethod move :default
  [animal]
  (str (:name animal) " skips down the yellow brick road!"))
````

Notice how the last multimethod has a key of `:default`. This basically tells
Clojure, "If you can't find a multimethod matching this key, use this one!"

In this next part, we'll create a few different animals that map to one of the 
multimethods we just created.

````clojure
(def donald {:name "Donald Duck"
             :kind :bird})
(def nemo {:name "Nemo"
           :kind :fish})
(def despereaux {:name "Despereaux"
                 :kind :rodent})
(def toto {:name "Toto"
           :kind :dog})
````

Now we can see how `move` behaves when we pass it each of the different animals.

````clojure
=> (move donald)
> "Donald Duck flies into the sunset!"
=> (move nemo)
> "Nemo swims through the ocean blue!"
=> (move despereaux)
> "Despereaux scurries through the grassy field!"
=> (move toto)
> "Toto skips down the yellow brick road!"
````

When we passed `toto` to `move`, Clojure uses the `:default` multimethod since
we don't have a method defined for `:dog`.

Pretty cool stuff, right? Of course, we probably wouldn't want to use a multimethod in
this example. This could be easily achieved in one function using `cond`, or
something similar. However, in cases where the implementation of a function 
vastly differs based on the data the function receives (:kind in this case), 
multimethods are a great tool to help separate ideas!
