---
layout: post
title:  "Function Composition with Partial"
date:   2022-02-11 7:52:05 -0400
categories: clojure apprenticeship
---

One thing I've found interesting about Clojure is how there are several ways of 
creating anonymous functions.

````clojure
=> (map (fn [n] (+ 5 n)) [1 2 3])
=> (map #(+ 5 %) [1 2 3])
=> (map (partial + 5) [1 2 3])
; ...
(6 7 8)
````

All of these methods are great, and some are more useful than others in different scenarios.
- The `fn` form gives you close control over your parameters.
- `#()` allows you to quickly define a function inline. This is useful when using
functions like `map` or `reduce` to operate on elements.
- `partial` takes an existing function and composes a new function based on some default
parameters that you give it.

Now it would seem that `#()` can do everything that `partial` can do, just with slightly 
more control and fewer characters. So why bother using `partial` at all? The `#()` form 
is much more readable (in my opinion).

I have a hypothesis, and I could be very wrong, but I believe this `partial` function
was created specifically for function composition, much like how `comp` is used.
Let's take a look at `comp` really quick.

````clojure
=> (defn plus-ten [n] (+ 10 n))
=> (defn divide-by-three [n] (/ n 3))
=> (def divided-by-three-plus-ten (comp plus-ten divide-by-three))
=> (divided-by-three-plus-ten 5)
35/3
````

Notice how `divided-by-three-plus-ten` is declared without parameters, yet it is a
function composed of two other functions. Similarly, I think the `partial` function 
was mainly intended for this sort of use case. 

````clojure
=> (defn log [level message] (println level "|" message))
=> (def info (partial log "Info"))
=> (def warn (partial log "Warn"))
=> (def error (partial log "Error"))

=> (log "Debug" "Some debugging")
"Debug | Some debugging"
=> (info "Some information...")
"Info | Some information..."
=> (warn "Hmm... this is fishy")
"Warn | Hmm... this is fishy"
=> (error "MAN OVERBOARD!!!")
"Error | MAN OVERBOARD!!!"
````

And there you have it! Function composition using `partial`. While we can just as easily
do the same thing with any of the other forms...

````clojure
(defn info [message] (log "Info" message))
(def warn #(log "Warn" %))
(def error (fn [message] (log "Error" message)))
````

...I think `partial` does a good job of getting this idea of function composition across; 
these are all functions composed of other functions. But whatever the original intent 
of the `partial`, `#()`, or `fn` forms were, always choose the one ***you*** think looks 
best for the code your writing!
