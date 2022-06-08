---
layout: post
title:  "Quotients"
date:   2021-10-20 7:52:05 -0400
categories: clojure, apprenticeship
---

A lot of times, it helps to check if there's a function for even the most basic 
operations when learning a new language. I just found out that Clojure has a 
function for integer division, and I've been doing it the hard way this whole time!

### How I've been doing it

I've just been doing normal division, then converting the result to an int. While this
works fine, it has a couple problems. First is clunkiness; if I decide to use this in 
my high-level functions, they can quickly get polluted with the integer division logic.

````clojure
(defn oreos-per-student []
  (let [oreos 10
        students 3]
    (int (/ oreos students))))
````

Of course, this could be easily solved by just creating a function for integer 
division, but that's where the second problem comes in. What on earth would I name
an integer division function? `div-int`, `int-div`, `integer-division`? It almost 
seems better to just leave the logic in the higher-level function.

### How Clojure does it

Clojure as a much more elegant way of performing integer division. `quot` (not to be 
confused with `quote`) refers to the "quotient" of two numbers, and will perform
the integer division we need. So our new and improved function now looks like this:

````clojure
(defn oreos-per-student []
  (let [oreos 10
        students 3]
    (quot oreos students)))
````

### What is a Quotient?

You may be asking yourself, "Isn't the quotient of a number just regular division?"
Well, yeah... it is. But words are hard, so the quotient used here is taken in a 
different context.

In mathematics, the quotient of 7 and 2 is 3.5. In that sense, the quotient would 
just be regular division. But we could also say that 7 divided by 2 gives us a 
_quotient_ of 3 with a _remainder_ of 1. This is the context that quotient is being 
used in; it takes the integer part of the division and drops the remainder.

### Is it Fast?

I haven't measured the speed, but I would think that `quot` would be faster than 
performing normal division and casting to an int. Firstly, both `/` and `quot` pass 
the operands directly into lower-level Java functions for their operations, which
in turn pass the operands to even lower-level functions.

If we assume that `quot` and `/` perform at _approximately_ the same speed, then 
adding an integer cast on top of that would only slow things down.

Additionally, I'm sure that integer casting was on the table when the original 
developers were writing the `quot` function, so there's probably a reason they favored 
the use of Java's quotient function over integer casting.

### Edit: More on Performance

A coworker of mine created a [benchmark][benchmark-code] function and sent me
the results of the different division methods discussed above. Check out his 
[blog post][benchmark-blog] on how the function works! It's very useful for a 
lot of scenarios.

In his results, we can see that `quot` is almost four times faster than `/`!
Crazy, right? I would have thought these would be just about the same. Not only 
that though, `quot` is almost seven times faster than our original 
integer-division method! This can make a _huge_ difference when executing
an operation millions, or even hundreds of thousands of times.

````clojure
(perf/benchmark 10 100000 "(int (/ 101 10))" #(int (/ 101 10)))
(perf/benchmark 10 100000 "(/ 101 10)      " #(/ 101 10))
(perf/benchmark 10 100000 "(quot 101 10)   " #(quot 101 10))
````

````
## (int (/ 101 10)) - 10 x 100000 ops, average per-op: 183ns
## (/ 101 10)       - 10 x 100000 ops, average per-op: 96ns
## (quot 101 10)    - 10 x 100000 ops, average per-op: 27ns
````

[benchmark-blog]: https://michaelwhatcott.com/benchmarking-clojure-code/
[benchmark-code]: https://github.com/mdwhatcott/advent-of-code/blob/main/clj/src/aoc/perf.clj
