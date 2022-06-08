---
layout: post
title:  "Functions vs Macros"
date:   2021-11-17 7:52:05 -0400
categories: clojure, apprenticeship
---

Macros have some clear differences from functions, but there is one more subtle 
difference that isn't quite obvious at first.

I read some more on [Mastering Clojure Macros][mastering-macros], 
and it pointed out something very important about macros: Macros are not values.

In Clojure, you can treat functions equally with other values types: 
numbers, strings, collections, etc. But you cannot always treat macros
as functions.

### Filtering Numbers

I want a function that will only take numbers divisible by 5.
One way I might implement this is to create a function that 
checks for 5's divisibility, then pass that function into a `filter`.

````clojure
user=> (defn divisible-by-5? [n] (= 0 (rem n 5)))
#'user/divisible-by-5?

user=> (filter divisible-by-5? '(1 5 2 7 10 4 15 19))
(5 10 15)
````

Now what if I decided to use a macro for the divisibility function instead?
How would that work? Let's try it out.

````clojure
user=> (defmacro divisible-by-5? [n] `(= 0 (rem ~n 5)))
#'user/divisible-by-5?

user=> (filter divisible-by-5? '(1 5 2 7 10 4 15 19))
Syntax error compiling at (REPL:1:1).
Can't take value of a macro: #'user/divisible-by-5?
````

What happened here? "Can't take value of a macro"? Hold on... maybe I missed 
something. Let me try something simpler.

````clojure
(defn do-nothing [_])
#'user/do-nothing

(defmacro nothing-macro [_])
#'user/nothing-macro

user=> (do-nothing nothing-macro)
Syntax error compiling at (REPL:1:1).
Can't take value of a macro: #'user/nothing-macro

user=> (do-nothing ->)
Syntax error compiling at (REPL:1:1).
Can't take value of a macro: #'clojure.core/->
````

### `(> functions macros)`

It seems that no matter what we try, we cannot pass macros to other functions.
This isn't exclusive to function parameters, however. Macros cannot be used
as any data value.

````clojure
user=> [->]
Syntax error compiling at (REPL:0:0).
Can't take value of a macro: #'clojure.core/->

user=> (defn get-thread-first [] ->)
Syntax error compiling at (REPL:1:1).
Can't take value of a macro: #'clojure.core/->
````

Functions, on the other hand, can be used just about anywhere.

````clojure
user=> [do-nothing]
[#object[user$do_nothing 0x78ed7141 "user$do_nothing@78ed7141"]]

user=> (defn get-my-func [] do-nothing)
#'user/get-my-func
````

In Mastering Clojure Macros, the author stresses the use of functions in place
of macros, whenever possible. This is because functions are simpler, easier 
to debug, and there are fewer surprises to people who use the function.

Imagine if you wanted to `map` a `plus-ten` function over a collection, and it
turned out to be a macro! You'd have to rewrite the macro as a function, and then
hope everyone's code still works. This is all _after_ you've spent time looking
for the problem in your `map`.

Macros aren't all bad though. There are plenty of cases where macros 
are the best tool for the job.

[mastering-macros]: https://www.amazon.com/Mastering-Clojure-Macros-Cleaner-Smarter/dp/1941222226/ref=sr_1_3?dchild=1&keywords=mastering+clojure+macros&qid=1633757690&sr=8-3