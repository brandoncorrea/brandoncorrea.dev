---
layout: post
title:  "Prime Factors"
date:   2021-10-01 11:14:25 -0400
categories: euler, apprenticeship
---
I was working on [Euler Problem 3][euler-3] (find the max 
prime factor of a number) earlier today. I wrote a lot of 
tests for prime number functions, but ended up not using 
a single "prime" function.

### Starting Out

I started by creating a couple functions. First `prime?`, 
which tested whether a number was prime. Then `factors`, 
which returned all the factors of a number. Combining 
these two functions, I made a third function, 
`prime-factors`. I thought to myself, "All I need to do 
now is return the max value from this collection, and 
I'll have my answer!"

I finally get to the last test: find the greatest prime 
factor of 600851475143. The moment of truth. I write the 
test and wait... and wait... and the test never fails... 
or passes. "That doesn't look good." The number being 
tested is six-hundred-billion, which I did not fully 
understand at first sight. The program is trying to iterate
over half a trillion numbers, while testing for prime factors.

"Easy! All I need to do is just shorten the number of
values I'm iterating over," I thought. Even starting at
a halfway point counting down, I'm still running through
about three-hundred-billion values.

### The Solution

How on Earth do we solve this? Consider the prime 
factorization of 60: 2, 2, 3, 5. We can see in this example 
that 5 is the greatest prime factor, but how did we get 
this list to begin with? Well, we start at 2 and divide 
until the number can no longer be divided evenly.

`60 / 2 = 30`

`30 / 2 = 15`

`15 / 2 = 7.5`

We can no longer divide by 2 and are left with 15. 
So let's try to divide by 3.

`15 / 3 = 5`

`5 / 3 = 1.66`

We can no longer divide by 3 and are left with 5. 
Let's divide by 4.

`5 / 4 = 1.25`

We cannot divide by 4 and are left with 5.
Let's divide by 5... Wait! The remainder is equal 
to our the divisor!

### Solution Explained

When I came to this realization, I had to ask myself a few
questions:
1. Why bother dividing by 4, an even number which we know is not prime?
2. How did we get the correct answer without checking for primes anywhere?
3. Will this process always yield a prime number?

The process of checking the validity of a prime comes with 
a lot of overhead compared to checking the divisibility of
two numbers. Allowing the algorithm to divide by 4 (or any 
other non-prime) is much more efficient than verifying it 
is a prime before dividing.

Let's look back at our prime factorization of 60 (2, 2, 3, 5).
Each time we divide 60, we remove a number from that list.
This is the essence of the process: to remove prime factors 
until only one is left. Since we are starting from the 
lower bound, we will end up with the greatest prime number.

An irrelevant but interesting thing to note is that if we 
were to start from the upper bound (N - 1), we would still 
end up with a prime number. However, we could not guarantee 
that it would be a greatest or least prime.

### Edit, 10/2/2021
I was mistaken on that last note. If you start the process 
from the upper bound (N - 1), then you would end up with
the smallest prime number. This is because the first divisor
reached would be the product of all but the smallest prime
factor.

In the example of 60, the first divisor would be 30 
(2 x 3 x 5). This would leave us with the smallest prime 
number of 2.

[euler-3]: https://projecteuler.net/problem=3