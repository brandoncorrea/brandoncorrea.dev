---
layout: post
title:  "Quotes are Hard (at first)"
date:   2021-10-13 7:52:05 -0400
categories: clojure, apprenticeship
---

You know that key that nobody ever uses ... ever? Well Clojure uses it, so get 
ready to start exercising some muscle memory! Clojure uses some different
quoting syntax that gives the programmer more control over how and when 
symbols are evaluated, which is especially useful when writing macros.

## [The Quote][quote] `'`

Going back to the basics, the `quote` returns the unevaluated list that follows.

````clojure
=> '(1 2 3 4 5)
(1 2 3 4 5)
=> '(1 2 3 (inc a) 5)
(1 2 3 (inc a) 5)
````

## The Syntax Quote `` ` ``

The syntax quote does basically the same thing as `quote`, except it's a bit 
more explicit when referencing symbols.

````clojure
=> `(1 2 3 (inc a) 5)
(1 2 3 (clojure.core/inc user/a) 5)
````

As we can see in this example, the syntax quote fully qualifies all our symbols
to include their namespaces. This can be useful, as the name `a` (or any 
other symbol) may be used in other parts of our program that can conflict 
with what we expect of it.

## [The Unquote][unquote] `~`

When used within a syntax quote, `unquote` will evaluate the symbol or form 
that follows.

````clojure
=> (def a 4)
#'user/a
=> `(1 2 3 a 5)
(1 2 3 user/a 5)
=> `(1 2 3 ~a 5)
(1 2 3 4 5)
=> `(1 2 3 ~(inc a) 5)
(1 2 3 5 5)
````

In our unquote examples, `~a` evaluated to 4 in our final result, and `~(inc a)`
evaluated to 5. This is good to know because a lot of the time when writing 
macros, we actually want what's inside a symbol, rather than the symbol itself.

The unquote will not work inside a regular `quote`, however. This is another 
way that the `quote` and syntax-quoting differ.

````clojure
=> '(1 2 3 ~a 5)
(1 2 3 (clojure.core/unquote a) 5)
````

## [Unquote-Splicing][unquote-splicing] `~@`

`unquote-splicing` has one job, and it does it well. When used inside a 
syntax-quoted list, it will simply take the values in a collection and place 
them in the parent collection. Let's compare `unquote-splicing` to `unquote`
in these cases.

````clojure
=> (def some-numbers [2 3])
#'user/some-numbers
=> `(1 ~some-numbers 4 5)
(1 [2 3] 4 5)
=> `(1 ~@some-numbers 4 5)
(1 2 3 4 5)
````

If we macroexpand this, we can see what's really is Clojure's is simply taking
everything in the list and `concat`-ing them together into one list. Cool!

````clojure
=> (macroexpand '`(1 ~@some-numbers 4 5))
(clojure.core/seq (clojure.core/concat (clojure.core/list 1) some-numbers (clojure.core/list 4) (clojure.core/list 5)))

;; And the simplified macroexpansion...
(seq (concat '(1) some-numbers '(4) '(5)))
````

## Quote Debugging

Now that we have some of the different quoting syntax out of the way, let's 
complicate things.

### `` `~some-symbol ``

Can you imagine what this might be doing? Well, we're syntax-quoting
`~some-symbol`. So if `~` just evaluates what follows when in a syntax quote, 
then what we should see is an evaluation of `some-symbol`. So `` `~ `` 
should behave like an identity (not to be confused with the identity function).

````clojure
=> (def some-symbol "Cymbal")
#'user/some-symbol
=> `~some-symbol
"Cymbal"
````

### `` `'~some-symbol``

What we are looking at here is the syntax-quoted form, `'~some-symbol`.
Since we are in a syntax-quoted form, we can go ahead and evaluate 
`~some-symbol` and what we should see is its quoted value.

````clojure
=> `'~some-symbol
(quote "Cymbal")
````

A lot of this can seem confusing at first, but once you get a handle on what
each quote does, it becomes easier to predict how it will behave when in the
presence of other quotes.

[quote]: https://clojuredocs.org/clojure.core/quote
[unquote]: https://clojuredocs.org/clojure.core/unquote
[unquote-splicing]: https://clojuredocs.org/clojure.core/unquote-splicing