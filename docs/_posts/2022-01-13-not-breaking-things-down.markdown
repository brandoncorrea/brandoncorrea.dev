---
layout: post
title:  "Not Breaking Things Down"
date:   2022-01-13 7:52:05 -0400
categories: clojure, apprenticeship
---

Sometimes it's best not to break things down in smaller pieces.
I've started implementing new Clojure Reagent components by taking the 
entirety of the design ("lorem ipsum" and all), and building functionality 
directly off of that.

I had originally thought to break it down into chunks, and _then_ implement
the functionality, but this ended up taking quite a bit of time. How should I 
break it down? Do any components require precedence? How can I ensure that the
end results turns out exactly like the wireframe?

So I have a hundred or so lines of Hiccup-type code that I need to add 
moving parts to that look something like this.

````clojure
[:div.container.center
 [:div.margin-left.padded
  [:h1.title.center "Hello, world!"]
  [:p.block-text "Lorem ipsum..."]
  ; ... more components ...
  ]]
````

After pasting the markup right into my source code, I had the freedom to jump 
around a bit. I could choose to work on the footer first, then jump to the
top to work on a button, then go back to the center of the page for another
feature. This limits the time spent asking myself, "what should I work on next?"

I don't really need to worry about precedence too much–if I run into
any roadblocks pertaining to the HTML structure, I can just go to wherever
that preceding item is needed and implement it.

If I need to visually see something in context, I have all the filler content 
to back me up. This also makes it harder to get lost or lose your place since
rest of the markup will usually have a good indication of where you're at.

Another thing that's kinda nice is that, while you probably wouldn't want to
deploy a half-completed page, at least it would _look_ good if you did!
