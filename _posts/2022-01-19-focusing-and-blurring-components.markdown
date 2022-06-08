---
layout: post
title:  "Focusing and Blurring Components"
date:   2022-01-19 7:52:05 -0400
categories: clojure, apprenticeship
---

Today I was trying to add an event listener to a Reagent component
that would fire when it lost focus, but just couldn't figure out how to do
that. Eventually someone was able to help me out, and the solution was a 
lot simpler than I had expected.

There were several possible event listeners that could be used here:
focus, blur, focusin, and focusout.

As you may have guessed, the [focus][onfocus] and [focusin][onfocusin] events
are fired when the target element receives focus (like clicking into a textbox). 
That leaves us with [blur][onblur] and [focusout][onfocusout], which are fired
when the target element loses focus (clicking out of a focused textbox).

So why are there two different events for focusing and blurring an element?
Well, according to Mozilla the focusin and focusout events will "bubble" 
while blur and focus do not.

Alright so what does bubbling even mean? [Bubbling][bubbling] just means that
when the event is triggered, it will also trigger on all parent elements
until it finally reaches the DOM.

At first, I had just tried adding an `:on-focusout` attribute to the component,
but that didn't seem to have any effect. It was as though it just wasn't
registering the attribute. I even tried different spellings, (`:onfocusout` 
and `:on-focus-out`) but tht didn't seem to do anything. Of course, what
I really needed to use was this event's non-bubbling counterpart: `:on-blur`.

Let's try something out on a text input. We'll just log to the console to 
see when our event fires.

````clojure
(defn title-input []
  [:input 
    {:type  "text"
     :blur  (fn [] (println "Title has been blurred!"))
     :focus (fn [] (println "Title has been focused!"))}])
````

If you add this component to a ClojureScript project, you should see that 
when you click into the textbox, the focus event is fired and the "focused"
text is logged to the console. Similarly, when you click out of that textbox,
you should only see the "blurred" text logged to the console.

While I couldn't get the bubbling events to work on a component, they weren't
what I actually needed–I only needed one component to know about the blur
event. If I had used a bubbling event, then that would have caused additional
processing on the DOM, and potentially unwanted side effects!

[onblur]: https://developer.mozilla.org/en-US/docs/Web/API/Element/blur_event
[onfocus]: https://developer.mozilla.org/en-US/docs/Web/API/Element/focus_event
[onfocusin]: https://developer.mozilla.org/en-US/docs/Web/API/Element/focusin_event
[onfocusout]: https://developer.mozilla.org/en-US/docs/Web/API/Element/focusout_event
[bubbling]: https://en.wikipedia.org/wiki/Event_bubbling