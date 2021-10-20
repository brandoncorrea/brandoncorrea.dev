---
layout: post
title:  "Code is Data"
date:   2021-10-08 7:52:05 -0400
categories: clojure apprenticeship
---

"Code is Data." I've seen this before somewhere in my journey through Clojure,
but this idea completely went over my head the first few times I encountered
it.

I've started reading a new book on Clojure 
([Mastering Clojure Macros][mastering-clojure-macros], if you're interested) 
and the very first thing the author tells the reader is that Code is Data. 
Now I'm curious to find out what this really means. I mean, surely functions 
and loops can't be the same thing as numbers and string... right?


### And I `'quote`

You may be familiar with the [quote][quote] syntax in Clojure, which makes it 
easy to create a list. Well, all a quote does is return an unevaluated form.
Which is why when we create lists with quote, we get the same stuff we put 
into it.

````clojure
=> '(1 2 3 4 5)
(1 2 3 4 5)
````

If we just go by that definition, "return an unevaluated form," then that means
we could probably do something like this.

````clojure
=> '(1 2 3 (inc 4) 5)
(1 2 3 (inc 4) 5)
````

What happened here? Why didn't `(inc 4)` evaluate to 5? Well, that's because 
`quote` returns an unevaluated form. This shouldn't be a completely foreign 
concept though. This is the same way we'd create a 2D list, `'((1 2) (3 4))`.

So since Clojure doesn't seem to care what _type_ of data we give it, we can
basically get it to return us a list of anything! Like this totally unfamiliar 
list:

````clojure
=> '(defn exclaim! [phrase] (str (.toUpperCase phrase) "!!!!!!!!!!!!"))
(defn exclaim! [phrase] (str (.toUpperCase phrase) "!!!!!!!!!!!!"))
````

Some people may call this a function, others would call it a list. What is it
exactly? Well, technically it's just a list. But if we `eval`-uate this list, 
then we would get a function to `exclaim!` things!

````clojure
=> (eval '(defn exclaim! [phrase] (str (.toUpperCase phrase) "!!!!!!!!!!!!")))
#'exclaim!
=> (exclaim! "hangry")
"HANGRY!!!!!!!!!!!!"
````

This is how Clojure sees your code; just a bunch of lists containing random 
data and lists of lists of more data. It isn't until Clojure _evaluates_ the 
lists that it can decide how it wants to handle them.

### Cheating in Clojure

So why does it matter that everything is a list for us Clojurists? Well, all
the functions that are available for any other list is available to us when 
working with these quoted lists.

For example, we can `drop`, `concat` and `conj` stuff, and do something silly 
like this:

````clojure
=> (def some-list '(+ 1 2 3 4 5))
#'some-list
=> (drop 1 some-list)
(1 2 3 4 5)
=> (concat some-list [6 7 8])
(+ 1 2 3 4 5 6 7 8)
=> (conj some-list '*)
(* + 1 2 3 4 5)
=> (conj (concat (drop 1 some-list) [6 7 8]) '*)
(* 1 2 3 4 5 6 7 8)
=> (eval (conj (concat (drop 1 some-list) [6 7 8]) '*))
40320
````

So what just happened here? Well, we just turned the sum of the first five 
numbers into the product of the first eight. If we can look at Clojure code 
as data, it makes it easier to visualize how we can manipulate it and bend 
it towards our will.

When I first read this in the book I thought, "That's cheating! Us programmers 
shouldn't be able to do this. What kind of hackery is this?" This simply 
the nature of Clojure, so we may as well take full advantage of it. And we do,
using macros! 

The visualization of code as data and knowing how we can manipulate it is how 
we create macros, which I'll leave as a topic for another day (mostly because
I don't know much about those things yet).

[mastering-clojure-macros]: https://www.amazon.com/Mastering-Clojure-Macros-Cleaner-Smarter/dp/1941222226/ref=sr_1_3?dchild=1&keywords=mastering+clojure+macros&qid=1633757690&sr=8-3
[quote]: https://clojuredocs.org/clojure.core/quote
