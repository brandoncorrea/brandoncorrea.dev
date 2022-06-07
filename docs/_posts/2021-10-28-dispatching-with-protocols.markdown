---
layout: post
title:  "Dispatching with Protocols"
date:   2021-10-28 7:52:05 -0400
categories: clojure, apprenticeship
---

I was working on adding a "Computer" player to my Tic Tac Toe game and needed 
some way to dispatch functionality for making a player's turn. I need to 
support both human-versus-human and human-versus-computer games.

There are a few different ways this can be done. One way would be to pass a map
down with the associated function. So something like this might work, but
I don't like how this looks when we need to use the `:next-move` function.

````clojure
(defn ->player [token next-move]
  {:token token 
   :next-move next-move})

(defn play-game [player-1 player-2]
  ;... Initialize player and game-board
  ((:next-move player) game-board (:token player))
  ;...
  )

(defn -main[]
  ;...
  (if player-vs-player?
    (play-game (->player \X request-player-move)
               (->player \O request-player-move))
    (play-game (->player \X request-player-move)
               (->player \O request-computer-move)))
  ;...
  )
````

This even has its drawbacks, however. For the computer's turn, the next-move
method needs to know about both player tokens, whereas in the human's turn
we need only know about that player's token. 

If we decided to go this route, then our "next-move" function declarations 
would look something like this:

````clojure
(defn request-player-move [board token _] ...)
(defn request-computer-move [board token opponent] ...)
````

We're not even using the second parameter, but we need it because of the 
way we're calling it from at a higher-level function... that's no good.

### Dispatching via Protocols

Clojure has a useful tool called [protocols][protocols], which you can think 
of almost as an interface or abstract class in OO languages.

So to solve the dispatching problem, we could create a `Player` protocol
with a `next-move` method to get the player's next move, and `token`
to substitute for `:token` keyword we had in our maps earlier.

````clojure
(defprotocol Player
  (next-move [this game-board])
  (token [this]))
````

Next we need to create types that implement these interfaces. You can think 
of types like you would a class in an OO language. We'll call these
types `Human` and `Computer`.

````clojure
(deftype Human [token]
  Player
  (next-move [this board]
    ; Implementation ...
    )
  (token [_] token))

(deftype Computer [token opponent]
  Player
  (next-move [this board]
    ; Implementation ...
    )
  (token [_] token))
````

With these two types, we can now instantiate them in our main function and
depend on the `Player` protocol rather than the types.

````clojure
(defn play-game [player-1 player-2]
  ;... Initialize player and game-board
  (next-move player game-board)
  ;...
  )

(defn -main[]
  ;...
  (if player-vs-player?
    (play-game (->Human \X)
               (->Human \O))
    (play-game (->Human \X)
               (->Computer \O \X)))
  ;...
  )
````

Using protocols in our game, we are able to interface with subtypes using 
some predefined interface, and even define infinitely more Player types!

Now if we wanted a player that always loses, protocols will allow us to do 
that without worrying too much about how it will affect other parts of our 
code.

[protocols]: https://clojure.org/reference/protocols