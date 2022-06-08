---
layout: post
title:  "AI Delegation"
date:   2021-11-23 7:52:05 -0400
categories: clojure apprenticeship
---

As my Tic Tac Toe boards become more and more complex (4x4 and 3x3x3),
the default Minimax AI used for the 3x3 boards will no longer cut it.

I have three different difficulties:
- Easy
- Medium
- Hard

Easy just selects a random square, and may continue to do that for any
board. Medium and Hard, however, depend on the Minimax algorithm, which 
doesn't give much room for losing.

When used on a 3x3x3 board, the Hard AI (Minimax depth of 6) takes
too long to compute a result. Even the Medium AI (depth 3) can take a long 
time on the first couple of moves. Lowering these factors to 1 or 2 makes 
it almost pointless to use the algorithm.

One option that may be worth investigating here is delegating AIs based on
the board being used.

### AI Delegation

The first question I asked was "Where would delegation occur?"
This delegation should most definitely occur at a higher-level module.

Let's say I have this function in main:

````clojure
(defn create-ai [difficulty board]
  (cond (= :easy difficulty) (->easy-ai board)
        (= :medium difficulty) (->medium-ai board)
        :else (->hard-ai board)))
````

Now I want to change the way medium and hard operate based on the board 
they receive, but what if another client doesn't mind waiting for the
AI to place their moves? When medium and hard are updated, the client will 
be disappointed to find that they've lost features in these AI modules.

Instead of updating the AI, new AI should be created and the client can 
decide which to use.

````clojure
(defn create-ai [difficulty board]
  (let [dimensions (dimensions board)]
    (cond (= :easy difficulty) (->random-ai)
          (> dimensions 2)
            (if (= :medium difficulty)
              (->block-randomly-ai)
              (->block-always-ai))
          (= :medium difficulty) (->minimax-ai 3)
          :else (->minimax-ai 6))))
````

Here we have the client decided:
- Easy will always be random
- Medium and Hard will use Minimax on 1D or 2D boards
- Randomly decide to block for medium on more than 2D boards
- Always block for hard on more than 2D boards

This gives the client full control over which AI to use for various 
scenarios. If the client decides to use an unbeatable Minimax for
a 12-Dimensional board, they are free to do so while accepting all 
consequences it may bring.
