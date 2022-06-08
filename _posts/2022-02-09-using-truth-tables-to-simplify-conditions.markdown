---
layout: post
title:  "Using Truth Tables to Simplify Conditions"
date:   2022-02-09 7:52:05 -0400
categories: apprenticeship
---

One of my favorite class I got to take in college was Discrete Math.
This is a field of math that deals a lot with logical structures, 
which I often use in refactoring some of the more complex parts of the code.

Truth tables are a great way to visualize some of this complex logic.
Here we have two conditions, A and B, which can either be true (1) or false (0).
Then we have our logical operations, "and" (∧), "or" (∨), and "not" (').

To start, we take our conditions, A and B, and fill out all of their possible results
(true or false).

| A | B | A ∧ B (A and B) | A ∨ B (A or B) | A ∧ B' |
| 0 | 0 |  |  |  |
| 0 | 1 |  |  |  |
| 1 | 0 |  |  |  |
| 1 | 1 |  |  |  |

Next, we want to evaluate each logical expression.
- A ∧ B  : Both A and B are true
- A ∨ B  : Either A or B is true
- A ∧ B' : A is true and B is false

| A | B | A ∧ B (A and B) | A ∨ B (A or B) | A ∧ B' |
| 0 | 0 | 0 | 0 | 0 |
| 0 | 1 | 0 | 1 | 0 |
| 1 | 0 | 0 | 1 | 1 |
| 1 | 1 | 1 | 1 | 0 |

Now that we have our results, let's take a look at the code we
want to refactor.

````clojure
(defn complicated-logic [a b]
  (or (and a b)
      (or a b)
      (and a (not b))))
````

Maybe you can already see how to simplify this function, but just forget that for a moment.
Using the results from our previous truth table, we can add another row 
to solve for the `or` applied on each of the expressions. We're only 
looking for one of these three expressions to be true.

| A | B | A ∧ B (A and B) | A ∨ B (A or B) | A ∧ B' | (A ∧ B) ∨ (A ∨ B) ∨ (A ∧ B') |
| 0 | 0 | 0 | 0 | 0 | 0 |
| 0 | 1 | 0 | 1 | 0 | 1 |
| 1 | 0 | 0 | 1 | 1 | 1 |
| 1 | 1 | 1 | 1 | 0 | 1 |

Our final result exactly matches our "or" results from A ∧ B, and this expression 
cannot be simplified any further. However, had our results been something not already
defined in the truth table, we would have needed to construct an expression that provided
us those results.

Here is our simplified function using the results from the truth table.

````clojure
(defn complicated-logic [a b] 
  (or a b))
````

This will provide the same results as the last function, just in less code.
You may decide to rename this function to `uncomplicated-logic`,
or even remove the function altogether and replace its uses with `or`!

[discrete-math]: https://en.wikipedia.org/wiki/Discrete_mathematics