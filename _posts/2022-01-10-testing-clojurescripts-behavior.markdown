---
layout: post
title:  "Testing ClojureScript's Behavior"
date:   2022-01-10 7:52:05 -0400
categories: clojure apprenticeship
---

I've recently been introduced to a new way of testing ClojureScript code
using the [c3kit spec-helper][c3kit-spec-helper] library.

Here I just have a button that just increments a reagent atom when clicked.
Now how can we test this?

````clojure
; Source
(def my-atom (reagent/atom 0))
(defn inc-my-atom [] (swap! my-atom inc))
(def inc-atom-button
  [:input {:type "button"
           :id "add-atom-button"
           :style {:width "100%" :display "block"}
           :on-click inc-my-atom
           :value "Click Me!"}])
````

Originally I would have written a test similar to this, where I just
validate the defined element verbatim.

`````clojure
; Spec
(it "tests atom button"
  (should= [:input {:type "button"
                    :id "add-atom-button"
                    :style {:width "100%" :display "block"}
                    :on-click inc-my-atom
                    :value "Click Me!"}]
           inc-atom-button))
`````

Now if you look at the spec and the source, this is just duplicate code!
I'm not really testing anything – really this is just a definition of what
`inc-atom-button` is, which I could more easily see by looking at the source code.

What is it that I actually want to test here? I don't really care that it's an
`:input` `"button"`, or even what the `:style` or inner text is. All these 
things are volatile and can change from one iteration to the next.

What I want to validate with this that the `#add-atom-button` exists,
and after it's clicked the `my-atom` will is incremented by 1. Using
c3kit, we can write a test like this

````clojure
(it "increments atom when clicked"
  (should-select "#add-atom-button")
  (should= 0 @my-atom)
  (click! "#add-atom-button")
  (should= 1 @my-atom))
````

Now we've removed all the cosmetic details of the UI from the functional 
requirements of our button! If the button changes to a clickable `:div`,
or the color changes from green to blue, our test will not break.

[c3kit-spec-helper]: https://cljdoc.org/d/com.cleancoders.c3kit/wire/1.0.6/api/c3kit.wire.spec-helper
