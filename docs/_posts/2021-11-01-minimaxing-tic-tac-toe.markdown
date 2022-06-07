---
layout: post
title:  "Minimaximizing Tic Tac Toe"
date:   2021-11-01 7:52:05 -0400
categories: clojure, minimax, apprenticeship
---

I've been trying to get the [Minimax][minimax] algorithm to work with a Tic Tac Toe game
over the last few days, and finally found an implementation that works!

Originally, I had been using a numeric value for my evaluations and comparisons, but a couple scenarios 
had popped up during testing where the algorithm would not choose _best_ outcome. As it turns out, the 
same issue had also been occurring in my original JavaScript version of Tic Tac Toe.

I'll try my best to explain what the problem is and how it can be solved, with minimal confusion.

### The Problem

The scenarios in question are variations of these two grids:

````
|O X O|
|_ X _|
|_ O _|

|X O _|
|O X _|
|_ _ O|
````

For both grids, we are trying to choose the best move X can make. This means for the first grid we
want a cell on the middle row, and for the second grid we want a cell on either corner. The issue 
I kept running into is that for some variations of these grids, minimax was not selecting these cells.

In particular, this issue would arise any time more than one evaluation resulted in a draw. The simple 
number-based evaluations have no way of differentiating between a better or worse draw result.
What we need now is a more comprehensive evaluation and comparison.

### Evaluating a Cell

Let's start with the evaluation function. First, we need to evaluate the base case: Game Over.

We can define game results as follows:
- Win = 1
- Draw = 0
- Loss = -1

If we reach a terminating cell, then there is no history to be compared. We'll just want to return the
value associated with an empty history.
- For example, `[1 []]` would be returned for a win result.

If the cell is non-terminating, then we will want to associate this cell with the history of all its 
sibling evaluations; the evaluations it is compared with when determining the minimum or maximum. We 
just need to save each of these evaluations in-memory and pass them back up along with their numeric 
evaluation (0, 1, or 2).

This may look something like this, `0 [[0 []] [1 [[1 []]]]]`, where `0` is the result and
`[[0 []] [1 [[1 []]]]]` is the nested evaluation history. This can quickly get complicated 
as the number or potential moves increases.

### Comparing Values

The next thing to do is compare two arbitrary evaluations, A and B. The process
to get the greater result of A and B would look something like this:
- Is A equal to B?
    - Do neither A nor B have child values?
        - Return – A and B are equal
    - Does A have Children?
        - Set A to the Max of the children of A, using this same process.
    - Does B have Children?
        - Set B to the Max of the children of B, using this same process.
    - Repeat comparison using the new A and B values
- Is the value of A greater than B?
    - Yes? Return – A is greater
    - No? Return – B is greater

This process ignores all minimax rules and just checks for maximum values throughout the nested history.

A "less-than" function could be created using the same process, only with "less than" and "minimum"
functions instead.

Using this same process, we can create `minimum` and `maximum` functions to use in our main minimax
algorithm to minimize and maximize values.

### Testing the Grids

The grids I mentioned earlier each have four different variations (rotated once for each side of the board).
So we will need to test all eight variations to ensure our algorithm was implemented properly.

Because our original implementation worked for some variations, but not others, we want to ensure any 
future implementation does not miss "rotation" scenarios.

[minimax]: https://en.wikipedia.org/wiki/Minimax