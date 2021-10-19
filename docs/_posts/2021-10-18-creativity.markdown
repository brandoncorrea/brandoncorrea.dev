---
layout: post
title:  "Creativity"
date:   2021-10-18 7:52:05 -0400
categories: programming
---

There are no right answers. Of course, to say that 1 + 1 = 3 would be false. 
But what if we decided to place that in a context where 3 represents 2's 
value? Then we could say that 1 + 1 = 3, and that 1 + 2 equals the value we 
currently know 3 represents. We could even say 1 + 3 is equal to that same 
value.

I'm starting to see a new side of engineering–a side where colors blend with 
the black and white nature of logic and mathematics.

While it is an objective and proven fact that programming is fun, there are
still those who would disagree. How can we express creativity in our work and
_**bring people to the dark si**_–I mean... show others that programming 
_really_ is fun?

### Context

Consider the following functions:

````clojure
(defn sum [nums] (reduce + nums))

(defn score [rolls] (reduce + rolls))

(def ShakeFairy (comp first))
(defn HarvestFairyDust [Fairies]
  (loop [UnshookFairies Fairies
         DustTotal 0]
    (if-let [DustParticles (ShakeFairy UnshookFairies)]
      (recur (rest UnshookFairies) (+ DustTotal DustParticles))
      DustTotal)))
````

The first is a simple sum function. It takes some numbers and adds them all up.

The second looks very similar. We could assume that `rolls` represents some 
collection of numeric values and `score` gives us the sum. Given the context 
of a Bowling Game application, this function makes perfect sense.

The third and fourth functions are interesting... they don't follow Clojure's 
typical naming convention. Also, why did the author use "UnshookFairies" over 
"UnshakenFairies"? Does the author not know how to spell? It looks like these 
functions might belong in some Peter Pan adventure game, where we need fairy 
dust to fly.

If we really analyze these two functions, we see `HarvestFairyDust` is simply
adding up everything in `Fairies`. It's just about equivalent to 
`reduce + Fairies`, so why didn't the author just use that instead? Maybe the
author inherited this function and modified it as needed, but didn't see the 
simplification. Maybe the author was just having fun naming things. 
Maybe he was just writing a blog post.

### Correctness

Each of these functions will satisfy our needs for summing a collection of 
values. After the application is deployed, the client will never know the 
difference; they will never see the source code. 

The only people that might care is the development team. They may even get a 
kick out of it at first, until time comes to make changes to that function.

In any case, each of these functions are _correct_.

### Audience Matters

Depending on your audience, you may elect to use one function over another.
- The user only cares that the function works for the numbers 1 - 5
- The development team says it must work for all real numbers
- The test engineers say it must perform at a certain speed
- Your three-year-old niece says it must be named `Rainbow-Unicorn`

### Creativity Matters

In traditional architecture, the architect must design a building that is both
visually pleasing and adheres to all building codes and natural laws. He may 
use any creative freedoms he wishes.

In poetry or storytelling, any coherent combination of words the author can
think of may be used. In fact, the text can even be incoherent for the sake 
of creativity.

Software developers are architects and writers of a sort. It wouldn't kill 
us to be a bit creative with our code, though it may get us fired if we try 
to get _too_ creative. But as long as we follow the code of ethics set before us,
we are free to code in any manner we wish. So if it makes sense for the context 
and audience to name something `Rainbow-Unicorn`, then what's stopping us?
