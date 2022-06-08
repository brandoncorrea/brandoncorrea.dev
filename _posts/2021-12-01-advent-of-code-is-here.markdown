---
layout: post
title:  "Advent of Code is Here!"
date:   2021-12-01 7:52:05 -0400
categories: clojure, apprenticeship
---

[Advent of Code][advent-of-code] has arrived! A 
[coworker][michael-whatcott-blog] of mine mentioned yesterday that 
these coding challenges were starting today–and this is the first 
year I'll be participating.

### Day 1, Part 1

The first challenge was pretty straightforward. You are given 2000 numbers
and asked to count the number of times a value increases from the one 
immediately before it.

For example, the collection below has 5 increases: 3, 6, 8, 19, and 19 again.
- `[5 2 3 6 6 4 8 19 18 19]`

#### Head-First Approach

At first, I approached the problem head on by incrementing a value each time
a number was increased:

````clojure
(defn- add-increases [[a n] b]
  (if (> b a)
    [b (inc n)]
    [b n]))

(defn increases [[first & nums]]
  (second (reduce add-increases [first 0] nums)))
````

This works by increasing a reduction only if the next number is greater than
the last. While this solution works fine as is, but I began experimenting 
with other solutions and found several others that require much less code 
and make more sense overall.

#### Summing 0s and 1s

This next solution works by pairing up adjacent values and mapping out
either a 1 (increase) or 0 (non-increase), then summing up the mapped values.

The problem with this solution is that inline function clutters things up,
making it a bit harder to read. Also, it doesn't really make sense to have
a bunch of random numbers (1 and 0) floating around, then summing those up.

````clojure
(defn increases [nums]
  (apply + (map (fn [[a b]] (if (< a b) 1 0)) (partition 2 1 nums))))
````

#### Enumerating Increases

This next form makes the most sense out of all other solutions I could
find. The problem asks "how many values increase?" This form answers that
question directly. "Count every partition where `a < b`."

````clojure
(defn increases [nums]
  (count (filter (fn [[a b]] (< a b)) (partition 2 1 nums))))
````

It took me way too long to realize this, but I don't even that inline 
function. If I just use the `<` function on its own, I get this...

````clojure
(defn increases [nums]
  (count (filter (partial apply <) (partition 2 1 nums))))
````

...and that is final form of the function. Filter out everything where the
first value is less than the second and count how many partitions remain.

### Day 1, Part 2

Part 2 of this problem wants us to sum up windows, and enumerate _those_ 
increases.

Using our earlier example, we would end up with 7 increases, where every 
value increases from the last:
- Original List: `[5 2 3 6 6 4 8 19 18 19]`
- Partitioned List: `[(5 + 2 + 3) (2 + 3 + 6) ... (19 + 18 + 19)]`
- Summed Partitions: `[10 11 15 16 18 31 45 56]`

Since we already have the last step taken care of with our function from 
Part 1, all we need to do is partition our list in 3 and sum up each of the
partitions, which we can do with the `apply +` and `partition` functions.

````clojure
(defn window-increases [size coll]
  (increases (map (partial apply +) (partition size 1 coll))))
````

And that's all! Moving some helper functions out into other modules
gives us our final solution:

````clojure
(defn increases [nums]
  (enumerate lt? (partition 2 1 nums)))
(defn window-increases [size coll]
  (increases (map sum (partition size 1 coll))))
````

[advent-of-code]: https://adventofcode.com
[michael-whatcott-blog]: https://michaelwhatcott.com