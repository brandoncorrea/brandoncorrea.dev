---
layout: post
title:  "Combinations"
date:   2021-10-19 7:52:05 -0400
categories: euler apprenticeship
---

Project Euler really forces you to think about problems in a different way.
I've been working on [Euler 12][euler-12] over the last couple of days and 
needed to find a way to count the factors of any given number.

### The First Implementation

Initially, I just approached the problem head-on. Just grab all the factors 
and count them all up, which worked for small numbers that were less than a 
couple hundred-thousand.

````clojure
(defn factors-of [n]
  (filter #(= 0 (rem n %)) (range 1 (inc n))))
````

Even after some refactoring and optimizations, this was still taking _way_ too
long. I needed something that would perform fast.

### Brainstorming

Every factor of a number must be reducible to one or more of its _prime_ 
factors. We already have a function to get prime factors (from Euler 1), 
so all that would be left to do is calculate all possible products from that 
list.

Well, that process doesn't sound very fun. Is there another way we could
count up all the factors of a number?

### Numbers are Symbols

With this problem, we can view each prime factor as a symbol like A, B, or C.
For example, lets look at the prime factorization of 12:

````
12 = 2 * 2 * 3
AAB = A, A, B
````

`A` now represents the value of 2 and `B` represents 3. Let's look at the 
factors of 12 and the unique combinations of `AAB`.

````
2 * 2 * 3 -> 2, 3, 4, 6, 12
AAB -> A, B, AA, AB, AAB
````

We see here that the number of factors is equal to the number of unique 
combinations of `AAB`. In fact if we took the product of each of the pairs,
where `A` represents 2 and `B` represents 3, then we would get the same list
of factors of 12!

### The Combination Function

Now there's a new problem to solve: Is there a function such that when given
a list of symbols (ex. A, A, B), it will return the number of unique 
combinations that can be made?

After fiddling with some numbers and drawing out tables for various lists, I 
came to some generic functions that would always work, but only for a
specific number of values.

Note: `A`, `B`, and `C` represent the number of times their symbols appear
in the list.

````
F(A) = A
F(A, B) = A(B + 1) + B
F(A, B, C) = A(B(C + 1) + C + 1) + B(C + 1) + C
````

We see some repetition in the third function, `B(C + 1) + C`, and that actually
looks a lot like our second function! Let's rewrite the third function to use 
the second. While we're at it, lets rewrite the second function to use the first.

````
F(A) = A
F(A, B) = A(F(B) + 1) F(B)
F(A, B, C) = A(F(B, C) + 1) + F(B, C)
````

### The Final Recursive Function

Each function passes the rest of the list into the function before it and does
some other arithmetic on that value. So our final function might look something
like this:

````
F(X, M..N) = X(F(M..N) + 1) + F(M..N)
````

`X` represents the next item in the list, `M..N` represents the rest of the 
list, and each list item is the number of times a symbol appears.

In the case of an empty list, `F` should return 0. So what we would have in 
the case of 12 (2, 2, 3) is the following:

````
Group (2, 2, 3) by their values to get (2, 1).

F(2, 1) = 2(F(1) + 1) + F(1)

F(1) = 1(F(..) + 1) + F(..) 
     = 1(0 + 1) + 0 
     = 1
     
F(2, 1) = 2(1 + 1) + 1 
        = 2 * 2 + 1 
        = 5
````

And voila, it works! Implementing this function in our Euler problem gives us 
our answer in just a couple seconds!

[euler-12]: https://projecteuler.net/problem=12