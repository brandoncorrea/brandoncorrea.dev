---
layout: post
title:  "Threading Macros"
date:   2021-10-04 23:11:22 -0400
categories: clojure, apprenticeship
---

I had the opportunity to play around with Clojure's threading macros today!
While I had read about them and have seen them here and there in code, I 
hadn't really understood how they worked.

There are a couple other threading macros, however I'll only talk about 
the thread-first and thread-last macros.

### Threading
Threading macros allow you to pass data down a chain of forms, which can
make your code more readable. In the example below, it's hard to tell what's 
happening when. But when we use the threading macro, we can clearly see that 
we start out with the number 1 and do some mathematical operations. We can
easily determine what value will be where simply by looking at the threaded form.

````clojure
=> (-> 1 inc (* 90 2) (/ 2) (+ 3 2) dec)
````

### Thread-First `->`
The [thread-first][thread-first] macro works by placing the preceding data at the beginning 
of the argument list of the next item in the chain. In the example below, 
we can see how the thread-first macro transforms our code.

As our data is passed down, it is placed at the beginning of the argument list,
right after the function name in the form. Thread-First makes each threaded 
argument the _first_ argument in the form.

````clojure
=> (macroexpand '(-> 1 inc (* 90 2) (/ 2) (+ 3 2) dec))
(dec (+ (/ (* (inc 1) 90 2) 2) 3 2))

=> (-> 1 inc (* 90 2) (/ 2) (+ 3 2) dec)
184
````

### Thread-Last `->>`

The [thread-last][thread-last] macro works exactly like the thread-first macro, except 
instead of placing arguments as the _first_ parameter, arguments are 
placed as the _last_ parameter.

Note how thread-last differs from thread-first in both it's expanded form
and the final result.

````clojure
=> (macroexpand '(->> 1 inc (* 90 2) (/ 2) (+ 3 2) dec))
(dec (+ 3 2 (/ 2 (* 90 2 (inc 1)))))

=> (->> 1 inc (* 90) (/ 2) (+ 3) dec)
721/180
````

### Order Matters (Sometimes)

It doesn't matter much which thread macro you use for single-arity functions,
sum, or product functions. However, when using forms where positional 
parameters matter, it's important to know where your parameters will end up 
in the final form.

In the example below, the thread-first macro produces an error while the 
thread-last macro gives us the sum of an array. This is because the thread-first
form is placing the array before the `+` sign, while the thread-last form places
it at the end where the `reduce` function expects it to be.

````clojure
=> (-> [1 2 3] (reduce +))
Execution error (IllegalArgumentException) at user/eval2036 (REPL:1).
Don't know how to create ISeq from: clojure.core$_PLUS_

=> (->> [1 2 3] (reduce +))
6
````

Threading is super cool, and now I must resist the urge to thread every
form I see, just because I can. It'd probably be easier to read the normal 
reduce form from the example anyway, and less prone to argument ordering 
mistakes.

[thread-first]: https://clojuredocs.org/clojure.core/-%3E
[thread-last]: https://clojuredocs.org/clojure.core/-%3E%3E
