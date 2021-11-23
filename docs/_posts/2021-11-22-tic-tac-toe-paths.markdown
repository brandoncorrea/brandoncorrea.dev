---
layout: post
title:  "Tic Tac Toe Paths"
date:   2021-11-22 7:52:05 -0400
categories: clojure apprenticeship
---

Today I was working on a 3-Dimensional Tic-Tac-Toe game and needed
to find a way to map out every possible path that can result in a win.

### Structural Overview

In a 3x3 board, the first cell would be represented by `[0 0]` and the 
last cell `[2 2]`. In a 3x3x3 board, this would be `[0 0 0]` and `[2 2 2]` 
respectively–each position in a key equates a dimension in the board.

In a 3x3, one possible path would be cells for the second row of the board, 
in no particular order:
- `[1 0] [1 1] [1 2]`

And in a 3x3x3, this would be the bottom row of the second dimension:
- `[1 2 0] [1 2 1] [1 2 2]`

In order to find every possible path, the path must follow these criteria:
- The number of cells must be equal to the size of the board (3 in a 3x3 4 in a 4x4).
- Each cell in the path must differ by:
  - Exactly 1 Position
  - Exactly 2 Positions
  - ...
  - Exactly D Positions (D is the number of dimensions in the board)

This seems simple at first, but it took me a bit to figure out how this 
would actually look.

### Finding the Differences

I thought it easiest to start out with checking when every position differs
and only comparing two cells.

````clojure
(defn differs-by-3? [[z1 y1 x1] [z2 y2 x2]]
  (and (not= z1 z2) 
       (not= y1 y2) 
       (not= x1 x2)))
````

That was easy enough. Now to checking for two and one position. 
This should be doable just by checking for equivalence of one item
and the "inequivalence" of one item, for each item.

````clojure
(defn differs-by-2? [[z1 y1 x1] [z2 y2 x2]]
  (or (and (= z1 z2)
           (not= y1 y2)
           (not= x1 x2))
      (and (not= z1 z2)
           (= y1 y2)
           (not= x1 x2))
      (and (not= z1 z2)
           (not= y1 y2)
           (= x1 x2))))

(defn differs-by-1? [[z1 y1 x1] [z2 y2 x2]]
  (or (and (not= z1 z2)
           (= y1 y2)
           (= x1 x2))
      (and (= z1 z2)
           (not= y1 y2)
           (= x1 x2))
      (and (= z1 z2)
           (= y1 y2)
           (not= x1 x2))))
````

These two functions look oddly similar. In fact, one might say that the
`differs-by-3?` function is a subset of the other two. It's not quite
clear yet how these can all be put into a single function, so I'll move on
to fixing the parameter issues.

### Variable Parameters

Ideally, this would work for any board size, so we should be able to pass 
in 1-N number of cells. We can do this using a rest parameter, `& cells`.
Since this will take away our parameter destructuring, it will need to be 
destructured in the function body.

````clojure
(defn map-3d-positions [cells]
  [(map first cells)
   (map second cells)
   (map last cells)])

(defn differs-by-...? [& cells]
  (let [[zs ys xs] (map-3d-positions cells)]
       ;...
       ))
````

Now that we've fixed the parameter, we can `apply` `=` or `not=` to all
the Xs, Ys, and Zs to find what we need.

````clojure
(defn differs-by-1? [& cells]
  (let [[zs ys xs] (map-3d-positions cells)]
    (or (and (apply not= zs)
             (apply = ys)
             (apply = xs))
        (and (apply = zs)
             (apply not= ys)
             (apply = xs))
        (and (apply = zs)
             (apply = ys)
             (apply not= xs)))))
````

The other two functions will look very similar, but we still need to find
out how to make these functions more generic.

### Consolidating the Functions

I don't know why I didn't see it before, but if we simply `count` the number
of positions that differ, then that will tell us the number of positions
the cells differ by.

````clojure
(defn differs-by-1? [& cells]
  (= 1 (count (map (partial apply not=) (map-3d-positions cells)))))
(defn differs-by-2? [& cells]
  (= 2 (count (map (partial apply not=) (map-3d-positions cells)))))
(defn differs-by-3? [& cells]
  (= 3 (count (map (partial apply not=) (map-3d-positions cells)))))
````

This change collapses all of these larger functions into one generic 
function.

````clojure
(defn differs-by-n? [n & cells]
  (= n (count (map (partial apply not=) (map-3d-positions cells)))))
````

Now that I have this taken care of, next is to figure out how to actually
get each possible path.
