---
layout: post
title:  "Error Checking"
date:   2021-10-21 7:52:05 -0400
categories: clojure apprenticeship
---

I was working on a Tic-Tac-Toe game and needed to validate some user input.
My function ended up getting quite large, and I had some error checking 
that was really irrelevant to the main idea of the function. 

With many languages there's usually a try-catch method to help with these 
sort of things, and Clojure is no exception.

### Clunky Error Checking

The function's purpose is to verify that only two numbers (representing a row
and column) are received as input and that the cell that they reference holds
the value, zero.

````clojure
(defn valid-move? [board options]
  (let [size (count board)
        [row col] options]
    (and (= 2 (count options))
         (<= 0 row)
         (<= 0 col)
         (> size row)
         (> size col)
         (zero? (nth (nth board row) col)))))
````

As you can see, I have a lot of error checking here to ensure that `row` and 
`col` are within the bounds of the board. I also take the `size` of the board,
which is only used to help verify `row` and `col`.

And of course, this function is still error-prone. If a board is passed in 
with shorter sized rows than columns, we would still get an error.

### Catching Errors

When checking for every possible scenario is impossible or cumbersome, try-catch
is there to save the day! 

Really, all I need is a function that checks the board's row/column value and
ensures there are only two values. in the user's input. Something like 
this would be ideal:

````clojure
(defn valid-move? [board [row col & rest]]
  (and (empty? rest)
       (zero? (nth (nth board row) col))))
````

Of course, that doesn't handle out-of-bound inputs–this should return `false` 
in those scenarios. If we wrap this in Clojure's try-catch logic, we get the
final version!

````clojure
(defn valid-move? [board [row col & rest]]
  (try (and (empty? rest)
            (zero? (nth (nth board row) col)))
       (catch Exception _ false)))
````

Using the try-catch method here, we only see the information that we actually 
care about: `board`, `row`, `col`, and the `rest` of the user input, if any. No 
ugly error checking and straight to the point!