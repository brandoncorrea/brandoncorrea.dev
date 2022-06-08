---
layout: post
title:  "Path Permutations"
date:   2021-11-30 7:52:05 -0400
categories: euler, apprenticeship
---

Many of the problems in [Project Euler][project-euler] will hint toward 
some type of solution whether it be
[finding the N<sup>th</sup> prime][problem-7], 
[counting divisors][problem-12], or 
[determining paths in a grid][problem-15]. 

Often the brute force solutions are too resource-heavy for the 
problems–even the more intuitive solutions can take more time than you'd 
initially expect.

One thing I've started to notice with my Euler solutions is that the most
efficient solutions tend to circumvent the question entirely.

### Paths in a Grid

The problem for [Euler 15][problem-15] states:

    Starting in the top left corner of a 2×2 grid, and only being able to 
    move to the right and down, there are exactly 6 routes to the bottom 
    right corner.
    
    How many such routes are there through a 20×20 grid?

The question here is "how many?" This means we don't actually need to 
traverse a grid and enumerate every possible path. 

Also, the question only mentions 2x2 and 20x20 grids. So to make things 
easier, we can ignore non-square grids (1x2, 8x5, etc.).

The solution to this problem is just a question of how many _permutations_
there are. We only need to know which formula applies to this problem. 
Once we find this formula, its performance will be solely dependent on how 
long it takes to calculate factorials.

### Calculating Unique Values

Before we solve this problem, let's suppose we were working with this unique 
set of values: `A B C D E`. 

The number of combinations in a set of unique values is always `N!`, where N 
is the number of values in the set. This is because with each position, there
is one less option to choose from.
- `[(5 options) (4 options) (3 options) (2 options) (1 option)]`

So the number of unique combinations for `A B C D E` would be 
`5! = 5 x 4 x 3 x 2 x 1 = 120`.

### Calculating Repeating Values

Now suppose we needed to solve for a list of repeating values: `A A A A A`.
Right away we know that there is only one combination, since swapping any A
with another would produce the same path, but how is this calculated?

We would start this problem assuming there are only unique numbers and just
finding `N!`. In this case we have 5 values, so we would get `5!`.

Next we need to remove duplicates that were produced by this operation.
Because we have 5 As, we would divide this number by `5!`, which gives us 
`5! / 5! = 1`.

So for a list of repeating values, there is only one possible path.

### Solving the Problem

Since we can only go right (`R`) and down (`D`), the values in the paths will
just be `R` and `D`. We should also note that the number of times each value
appears in an `NxM` grid is `N` times for `D` and `M` times for `R`.

So our values for varying grid sizes would look like this:
- 2x2: `R R D D`
- 3x3: `R R R D D D`
- 3x5: `R R R R R D D D`

First we'll want to determine the number of paths if every value was unique.
In an `NxM` grid there are `N + M` values in each path, so the number of paths 
would be `(N + M)!`.

Just like we did for lists of repeating values, we will want to remove 
duplicate paths created by multiple occurrences of `N` and `M`. To do this,
we just divide `N!` and `M!`.

Our formula now is starting to look something like this:
- `(N + M)! / N! / M!`

Which can be simplified to:
- `(N + M)! / (N! M!)`

For our particular problem, this can be simplified even further with the 
fact that we will always have square NxN grids:
- `(N + N)! / (N! N!)` → (2N)! / N!<sup>2</sup>

Testing our formula out on a few different grids gives us:
- 1x1: `(2 * 1)! / 1!^2` → `2! / 1^2` → `2 / 1` → `2`
- 2x2: `(2 * 2)! / 2!^2` → `4! / 2^2` → `24 / 4` → `6`
- 3x3: `(2 * 3)! / 3!^2` → `6! / 6^2` → `720 / 36` → `20`

Finally, applying this to the 20x20 grid gives us our result!
No looping or filtering necessary.

[problem-7]: https://projecteuler.net/problem=7
[problem-12]: https://projecteuler.net/problem=12
[problem-15]: https://projecteuler.net/problem=15
[project-euler]: https://projecteuler.net