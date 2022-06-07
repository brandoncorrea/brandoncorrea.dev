---
layout: post
title:  "Managing State in Quil"
date:   2021-12-09 7:52:05 -0400
categories: clojure, apprenticeship
---

I've recently been working on adding a GUI using [Quil][quil] to my 
Tic Tac Toe project. One problem I ran into while implementing this
was managing state.

I start out with a structure like this:

````clojure
{:screen-height 500
 :screen-width  500
 :mouse         {:down-x nil :down-y nil :up-x nil :up-y nil}
 :page          :new-game}
````

As the application progresses, the structure will change. 
On the play page, for example, it might look something like this:

````clojure
{:screen-height 500
 :screen-width  500
 :mouse         {:down-x nil :down-y nil :up-x nil :up-y nil}
 :page          :play
 :play {:id 12
        :board {[0 0] nil [0 1] nil #_... }
        :player {:type :human :token \X}
        :next-player {:type :unbeatable :token \O}
        :results {:game-over false :draw false :winner nil}}}
````

If I wanted to do something if the current player was a human, 
then the function might end up looking like one of these:

````clojure
(defn do-something-if-human [state]
  (if (= :human (get-in state [:play :player :type]))
    #_...do_something))

(defn do-something-else-if-human [{% raw %}{{{type :type} :player} :play}{% endraw %}]
  (if (= :human type)
    #_...do_something_else))
````

This is bad for two reasons:
1. Your function is cluttered with the state's structural detail
2. If the structure changes, every dependent function must also change

What we want is an interface between our functions and the state that
allows us to access state properties without knowing anything about 
its structure.

The result of giving the state its own namespace resulted in a ton of 
functions that looked more or less the same, all using `get-in` to 
retrieve something from the state or `assoc-in` to set a property in the state.

So to address the problem above, I created these two functions for state:

````clojure
(defn player [state] (get-in state [:play :player]))
(defn human-turn? [state]
  (= :human (:type (player state))))
````

Now in my higher-level functions, I can call the `human-turn?` function on the state.
What's even better is I don't even need to know about `:human`! That is all handled 
by the state interface.

````clojure
(defn do-something-if-human [state]
  (if (human-turn? state)
    #_...do_something))
````

If my structure changes later on, and I have 100 other functions that rely on this 
function, all I will need to do is update the `human-turn?` function; all my other
functions will remain unaffected.

[quil]: quil.info