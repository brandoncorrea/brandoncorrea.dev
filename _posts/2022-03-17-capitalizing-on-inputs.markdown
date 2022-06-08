---
layout: post
title:  "Capitalizing on Inputs"
date:   2022-03-17 7:52:05 -0400
categories: clojure, apprenticeship
---

Today I was working with a team member on displaying the first letter of
a textarea as a capital. While there are [some tricks][stackoverflow]
with CSS you can use to achieve this using the `text-transform` attribute,
these tricks stop working at our textarea.

However, this minor setback didn't stop us! Rather than trying to style
this input with a first capital letter, we can just take what the user types
and capitalize that!

````clojure
(defn capitalize [text]
  (if (= 0 (count text))
    text
    (str (s/upper-case (subs text 0 1)) (subs text 1))))
````

With the first condition, we're handling the case where the string is empty.
This is because `(subs "" 1)` and `(subs "" 0 1)` will throw an out-of-bounds 
exception since it's looking for a string position that doesn't exist.

Now we can apply this function to our textarea to capitalize our strings!

````clojure
(defn- capitalized-textarea [{:keys [text]} _]
  (let [text-atom (reagent/atom (capitalize text))]
    (fn []
      [:textarea
       {:value     @text-atom
        :on-change #(reset! text-atom (capitalize (forms/eventv %)))}])))
````

[stackoverflow]: https://stackoverflow.com/questions/5577364/make-the-first-character-uppercase-in-css