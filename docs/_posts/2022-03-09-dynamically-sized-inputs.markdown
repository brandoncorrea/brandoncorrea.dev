---
layout: post
title:  "Dynamically Sized Inputs"
date:   2022-03-09 7:52:05 -0400
categories: clojure apprenticeship
---

Today I was working on resizing a textbox automatically size itself to its contents.
I initially thought I could just use JavaScript to 
[calculate the text width][calculate-width] and then set the height accordingly based
on the width of the textbox. However, that proved to be more complicated than 
I had anticipated. 

While this should work in theory, there are a couple things that get in the way of
a clean solution. 
1. The input size may change. I would have to save off the width of the textbox 
and then watch the page for resize events to track that width. 
2. The `word-break` style may be set to keep whole words intact. This means that there 
can be blank space at the end of each line that will need to be accounted for.

While logic _could_ be created for this, it would be resource heavy and require some
trial & error. Additionally, words can be broken by either a space or hyphen character.
So I'd have to do more research to find out what characters can break a word. Way
more work than I'd care to put into a resizeable textbox! There's gotta be a simpler 
way...

...and there is! 

`textarea` elements (the input type I was working with) are technically scrollable
containers. So any overflow text in a `textarea` can be scrolled to. Since this
text can be scrolled to, there *must* be some sort of `scrollHeight` value associated
with this element.

So if we just set the element's `height` to its `scrollHeight` on render and on update,
then that should simulate a responsive textbox!

````javascript
function onTextChange () {
    element = document.getElementById('my-textbox')
    element.height = `${element.scrollHeight}px`
}
````

Now there's just one problem with this: what happens when we delete text? It _should_
resize back down to its original height, but since we're setting the `height` to
the `scrollHeight`, and since `scrollHeigth` will always be greater than or equal to 
`height`, then the textbox will never shrink back down.

To fix this, all we need to do is set the height back to some initial minimum height
before setting it to the scroll height. Don't worry about the textbox height jumping
up and down every time the user types–this will happen so fast that the browser 
won't even see it.

````javascript
function resizeTextbox () {
    element = document.getElementById('my-textbox')
    element.height = '25px' // Default or initial textbox height
    element.height = `${element.scrollHeight}px`
}
````

And that's it! Invoking this function on render and on change should simulate a 
smooth and responsive textbox without the overhead of calculating text sizes or
managing event listeners!

### ClojureScript Solution

Below is the same solution using ClojureScript and a reagent class's component handlers.

````clojure
(ns example.core
  (:require
    [reagent.core :as reagent]
    [reagent.dom :as reagent-dom]))

(defn resize-textbox [node min-height]
  (let [style (.-style node)]
    (set! (.-height style) min-height) ; Set the height to a default or initial value
    (set! (.-height style) (str (.-scrollHeight node) "px"))))

(defn resize-wrapper [{:keys [min-height]} _]
  (let [resize #(resize-textbox (reagent-dom/dom-node %) min-height)] 
    (reagent/create-class
      {:component-did-mount  resize
       :component-did-update resize
       :reagent-render       (fn [_ component] component)})))

; Example Usage

(def description (reagent/atom ""))
(defn set-description [event] #_...)
(defn description-input []
  [resize-wrapper {:min-height "25px"}
   [:textarea
    {:value     @description
     :on-change set-description}]])
````

[calculate-width]: https://thewebdev.info/2021/02/22/how-to-calculate-text-width-with-javascript/