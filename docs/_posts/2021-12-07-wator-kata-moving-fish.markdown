---
layout: post
title:  "Wa-Tor Kata: Moving Fish"
date:   2021-12-07 7:52:05 -0400
categories: clojure, apprenticeship
---

A few days ago I wrote about some [different Katas][wator-blog] that could be done
with the [Wa-Tor][wator] simulation. I've decided on performing the Kata by 
implementing how the Fish choose their next move using the function: `chronon`.

THe Fish's move must follow these rules:
1. The chronon chosen must be a neighbor.
   - Adjacent to either a side or a corner of the current position.
2. The chronon chosen must not already be occupied.
3. If there are no chronons available, the fish remains at its current position.
4. If there are many chronons available, the fish must choose one at random.

Because I'm performing the Kata in an existing project that depends on this function,
I will be starting out with this stub of the function.

````clojure
(defn chronon [_ _])
````

The function's interface accepts an "ecosystem" (`eco`) as the first parameter. `eco` 
contains all chronons on the board as well as the chronons' occupants.

The second parameter is the position (`pos`) that we want to know about; what will be 
the next move from this position?

### The First Test

Since the first test should be a degenerate test, I will check that the current position
is returned when that is the only position on the board.

````clojure
; Spec
(it "Results in current chronon when no others exist"
  (should= [0 0] (chronon {[0 0] {:type :fish}} [0 0])))
; Source
(defn chronon [_ _] [0 0])
````

We can see that this will only ever pass for position `[0 0]`, so we'll test `[1 1]` next

````clojure
; Spec
(it "Results in current chronon when no others exist"
  (should= [0 0] (chronon {[0 0] {:type :fish}} [0 0]))
  (should= [1 1] (chronon {[1 1] {:type :fish}} [1 1])))
; Source
(defn chronon [_ pos] pos)
````

We see some duplication in our test code, so let's refactor that while our tests still pass.
We'll also pull out the fish map since we'll be using that quite often throughout the kata.

````clojure
(def fish {:type :fish})
; ...
(for [pos [[0 0] [1 1]]]
  (it (format "Results in %s when no others exist" pos)
    (should= pos (chronon {pos fish} pos))))
````

### Retrieving Unoccupied Cells

Next we'll test the next simplest rule: choose an unoccupied cell.

````clojure
; Spec
(it "Results in an unoccupied neighbor when available"
  (should= [0 1] (chronon {[0 0] fish [0 1] nil} [0 0])))
; Source
(defn chronon [eco pos]
  (or (ffirst (filter #(nil? (second %)) eco))
      pos))
````

At this point, our tests are passing again and it's time to refactor. This step
is very important between each test cycle. The longer we go without refactoring,
the harder it will be to refactor later on.

````clojure
(defn unoccupied-neighbor? [[_ v]] (nil? v))

(defn next-unoccupied-neighbor [eco]
  (ffirst (filter unoccupied-neighbor? eco)))

(defn chronon [eco pos]
  (or (next-unoccupied-neighbor eco)
      pos))
````

### Identifying Neighbors

One problem we see with our current code is that this doesn't check for neighbors; it only
checks that the chronon is unoccupied. Let's fix that...

````clojure
(it "Non-Neighboring chronons are ignored"
  (should= [0 0] (chronon {[0 0] fish [0 2] nil} [0 0])))
````

Hmm... this might be a bit much to test from our higher-level code. Let's 
comment this out for now and create a new function, `neighbor?`.

````clojure
; Spec
(it "[0 0] and [0 0] are not neighbors"
  (should= false (neighbor? [0 0] [0 0])))
; Source
(defn neighbor? [_ _] false)
````

Since this will fail for any cells that _are_ neighbors, let's test cells 
that actually are neighbors.

````clojure
; Spec
(it "[0 0] and [0 1] are neighbors"
  (should= true (neighbor? [0 0] [0 1])))
; Source
(defn neighbor? [p1 p2] (not= p1 p2))
````

After continuing to test using various positions, we form this code.

````clojure
; Spec
(describe "neighbor?"
  (for [[p1 p2] [[[0 0] [0 0]]
                 [[0 0] [0 2]]
                 [[0 2] [0 0]]
                 [[0 0] [2 0]]
                 [[2 0] [0 0]]]]
    (it (format "%s and %s are not neighbors" p1 p2)
      (should= false (neighbor? p1 p2))))
          
  (for [[p1 p2] [[[0 0] [0 1]]
                 [[0 1] [0 0]]
                 [[0 0] [1 0]]
                 [[1 0] [0 0]]]]
    (it (format "%s and %s are neighbors" p1 p2)
      (should= true (neighbor? p1 p2)))))
      
; Source
(defn neighbor? [[r1 c1 :as p1] [r2 c2 :as p2]]
  (and (not= p1 p2) 
       (<= -1 (- c1 c2) 1)
       (<= -1 (- r1 r2) 1)))
````

Now that we have our `neighbor?` function, we can uncomment and pass our `chronon` test!
This will require some changes to our existing helper functions as well.

````clojure
; Spec
(it "Non-Neighboring chronons are ignored"
  (should= [0 0] (chronon {[0 0] fish [0 2] nil} [0 0])))

; Source
(defn unoccupied-neighbor? [pos [p v]]
  (and (neighbor? pos p)
       (nil? v)))

(defn next-unoccupied-neighbor [eco pos]
  (ffirst (filter (partial unoccupied-neighbor? pos) eco)))

(defn chronon [eco pos]
  (or (next-unoccupied-neighbor eco pos)
      pos))
````

The last thing we'll do here is refactor `next-unoccupied-neighbor` using the thread-last macro.

````clojure
(defn next-unoccupied-neighbor [eco pos]
  (->> eco
       (filter (partial unoccupied-neighbor? pos))
       ffirst))
````

### Testing Randomness

The last requirement that we have yet to implement is choosing a random chronon 
when given more than one option. How do we even write a test for randomness?

One way we can do execute the function several times and put the results in a set.
Once we have that set, we can compare the results to a set containing all the results
we'd expect the function to return. 

For our test, we'll only check for two different values. We'll repeat the function 
by a factor of five to ensure we get all the expected results.

````clojure
; Spec
(it "Randomly chooses between multiple available chronons"
  (should= #{[0 1] [1 0]} (set (repeatedly 10 #(chronon {[0 0] fish [0 1] nil [1 0] nil} [0 0])))))
; Source
(defn next-unoccupied-neighbor [eco pos]
  (->> eco
       (filter (partial unoccupied-neighbor? pos))
       seq
       rand-nth
       first))
````

Our random test looks a bit long. We can use a threading macro to help this fit on the screen better.

````clojure
(it "Randomly chooses between multiple available chronons"
  (->> #(chronon {[0 0] fish [0 1] nil [1 0] nil} [0 0])
       (repeatedly 10)
       set
       (should= #{[0 1] [1 0]})))
````

Now we can start our Wa-Tor simulation and see how our fish start moving across the screen!

### Spec

````clojure
(def fish {:type :fish})

(describe "Wa-Tor Kata"
  (describe "chronon"
    (for [pos [[0 0] [1 1]]]
      (it (format "Results in %s when no others exist" pos)
        (should= pos (chronon {pos fish} pos))))
    (it "Results in an unoccupied neighbor when available"
      (should= [0 1] (chronon {[0 0] fish [0 1] nil} [0 0])))
    (it "Non-Neighboring chronons are ignored"
      (should= [0 0] (chronon {[0 0] fish [0 2] nil} [0 0])))
    (it "Randomly chooses between multiple available chronons"
      (->> #(chronon {[0 0] fish [0 1] nil [1 0] nil} [0 0])
           (repeatedly 10)
           set
           (should= #{[0 1] [1 0]}))))

  (describe "neighbor?"
    (for [[p1 p2] [[[0 0] [0 0]]
                   [[0 0] [0 2]]
                   [[0 2] [0 0]]
                   [[0 0] [2 0]]
                   [[2 0] [0 0]]]]
      (it (format "%s and %s are not neighbors" p1 p2)
        (should= false (neighbor? p1 p2))))
    (for [[p1 p2] [[[0 0] [0 1]]
                   [[0 1] [0 0]]
                   [[0 0] [1 0]]
                   [[1 0] [0 0]]]]
      (it (format "%s and %s are neighbors" p1 p2)
        (should= true (neighbor? p1 p2))))))
````

### Source

````clojure
(defn neighbor? [[r1 c1 :as p1] [r2 c2 :as p2]]
  (and (not= p1 p2)
       (<= -1 (- c1 c2) 1)
       (<= -1 (- r1 r2) 1)))

(defn unoccupied-neighbor? [pos [p v]]
  (and (neighbor? pos p)
       (nil? v)))

(defn next-unoccupied-neighbor [eco pos]
  (->> eco
       (filter (partial unoccupied-neighbor? pos))
       seq
       rand-nth
       first))

(defn chronon [eco pos]
  (or (next-unoccupied-neighbor eco pos)
      pos))
````

[wator-blog]: https://brandoncorrea.github.io/apprenticeship/2021/12/03/wa-tor.html
[wator]: https://en.wikipedia.org/wiki/Wa-Tor