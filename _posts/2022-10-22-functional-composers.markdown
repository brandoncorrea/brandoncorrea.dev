---
layout: post
title:  "Functional Composers"
date:   2022-10-22 7:52:05 -0400
categories: clojure
---

Clojure has a lot of neat features that you don't really find in 
other languages. Recently I've been exploring functions that create
and return other functions!

I find these to be very useful in eliminating unnecessary code and 
keeping functions "to the point". However, there are some things to 
be aware of when using these, which I will cover towards the end.

## [constantly][constantly]

While this isn't the most interesting composer, I thought this would
be good just to get our feet wet with the idea that functions can return 
functions.

`constantly` (as you may imagine) produces a function that
constantly returns the same value.

````clojure
=> (def fives (constantly 5))
=> (fives)
5
=> (fives nil)
5
=> (fives :any "arity" 'or :type 1)
5
````

This function will take _any_ value you give it and return that.

````clojure
=> ((constantly nil))
nil
=> ((constantly true))
true
=> ((constantly :doggo))
:doggo
=> ((constantly 'sim))
'sim
=> ((constantly identity))
#object[clojure.core$identity 0x17bf2f9a "clojure.core$identity@17bf2f9a"]
````

## [partial][partial]

`partial` will create a function with partially populated 
arguments. This can be useful when you know the values you expect for a
function.

For example, we know that `m` only has three values that _can_ be incremented: 
`:x`, `:y`, and `:z`.

````clojure
(defn inc-kw [kw m] (update m kw inc))
(def inc-x (partial inc-kw :x))
(def inc-y (partial inc-kw :y))
(def inc-z (partial inc-kw :z))

(defn update-winner [m]
  "The boring way... 😒"
  (cond
    (x-wins? m) (inc-kw :x m)
    (y-wins? m) (inc-kw :y m)
    (z-wins? m) (inc-kw :z m)))

(defn update-winner [m]
  "The fun way! 😆"
  (cond
    (x-wins? m) (inc-x m)
    (y-wins? m) (inc-y m)
    (z-wins? m) (inc-z m)))
````

You may decide to create a partial function that populates every argument.

````clojure
(defn place-brick [wall color size x y]
  (assoc wall [x y] {:color color :size size}))

(def center-small-red-brick (partial place-brick {} :red :small 5 5))
````

But there are better ways of doing this.

````clojure
(def center-small-red-brick #(place-brick {} :red :small 5 5))
(def center-small-red-brick (constantly {[5 5] {:color :red :size :small}}))
````

Be careful not to give your partial functions too many arguments... 
it doesn't like that, and won't tell you until you try to invoke the 
function.

````clojure
=> (def fake-id (partial identity :one :two))
=> (fake-id)
Execution error (ArityException) at user/eval2052 (REPL:1).
Wrong number of args (2) passed to: clojure.core/identity
````

On the other hand, You may be tempted to create a partial function 
without any partial arguments. However, this will simply return that 
same function back to you.

````clojure
=> (= identity (partial identity))
true
````

## [fnil][fnil]

Since we're already talking about pre-populated arguments, `fnil` is
another function composer that replaces `nil` arguments with a predefined 
value.

This can be useful when you need to perform some operation, but expect `nil`
values to be passed in. One way you might handle an add function (for example) 
may be like this.

````clojure
=> (defn safe-add [x y]
      (+ (or x 0) (or y 0)))
=> (safe-add nil 1)
1
=> (safe-add 2 3)
5
````

However, `fnil` allows you to handle these scenarios in a much nicer way!

````clojure
=> (def safe-add (fnil + 0 0))
=> (safe-add 2 nil)
2
=> (safe-add 3 4)
7
````

One way to look at `fnil` is a way to guarantee your function always has
_some_ value on every invocation.

Just be careful when trying to patch more than three arguments. `fnil` 
doesn't support this arity.

````clojure
=> (fnil + 1 2 3 4)
Execution error (ArityException) at user/eval2134 (REPL:1).
Wrong number of args (5) passed to: clojure.core/fnil
````

## [comp][comp]

`comp` is a really fun func! You can think of this one as a 
backwards thread operator.

These functions will do the exact same thing.

````clojure
(defn next-odd? [m] (-> m :next inc odd?))
(def next-odd? (comp odd? inc :next))
````

Notice how the functions are reversed in the `comp` function. That's 
because `comp` functions are evaluated from right to left. So when 
writing functions this way, be sure the order of the functions are 
correct, or you may run into some issues later on.

Finally, if you were curious:
- Zero arguments produces the `identity` function
- One argument returns the same function you provided

````clojure
=> (= identity (comp))
true
=> (= + (comp +))
true
````

## [some-fn][some-fn]

This is probably my favorite composer. `some-fn` is basically
a function version of `or`.

This function is useful when you want the first non-nil value of something,
or if you have an `or` condition that operates on the same value.

Suppose you have a function like the following.

````clojure
(defn rule-da-world [person]
  (when (or (leader? person) 
            (dictator? person)
            (has-red-button? person))
    ; Do da ruling
    ))
````

You'd probably extract that big `or` condition into another function.

````clojure
(defn can-rule-da-world? [person]
  (or (leader? person)
      (dictator? person)
      (has-red-button? person)))
````

This is a good first step, but we can take this even further with `some-fn`!

````clojure
(def can-rule-da-world? (some-fn leader? dictator? has-red-button?))

(defn rule-da-world [person]
  (when (can-rule-da-world? person)
    ; rule da world
    ))
````

What's nice about this is that we've completely removed the concept of `person`,
(or any argument name) from `can-rule-da-world?`! Now our argument need only to 
conform to `leader?`, `dicator?`, and `has-red-button?`.

Next is a function I use in finding the project id based on any entity it 
receives. We have a few assumptions here:
1. No projects have a `:project` key
2. Every other entity has a `:project` key
3. If a _thing_ doesn't have a `:project` or an `:id`, we can assume it's a project id

````clojure
(def project-id (some-fn :project :id identity))
````

First, we check for a `:project`. If we get a non-nil value, then that 
value is returned. If we get nil, then we check `:id`. If we get a non-nil value,
then that value is returned. If we get nil, then we just send back the original
argument. This can be the id, `nil`, or potentially something completely different!

Lastly, keep in mind that this function will short circuit on the first truthy value.

````clojure
=> (def throw-odd #(throw (Exception. (str "Odd: " %))))
=> (def throw-odds (some-fn even? throw-odd))
=> (throw-odds 2)
true
=> (throw-odds 3)
Execution error at user/throw-odd (REPL:1).
Odd: 3
````

## [every-pred][every-pred]

This function works almost the same as `some-fn`, just not as cool. 
Think of it as your functional `and`. If every function you provide it
results in a truthy value for the argument, then the function will 
return `true`.

````clojure
=> (def natural? (every-pred pos? integer?))
=> (natural? 3)
true
=> (natural? 3.1)
false
````

It's important to know that this function will _not_ return the value of 
the first falsy value or the last truthy value. It will always return a boolean.

````clojure
=> (def has-xyz? (every-pred :x :y :z))
=> (has-xyz? {:x 1 :y 2})
false
=> (has-xyz? {:x 1 :y 2 :z 3})
true
````

Like `some-fn`, this function will also perform short-circuit evaluation
on the first falsy value.

````clojure
=> (def throw-odd #(throw (Exception. (str "Odd: " %))))
=> (def throw-odds (every-pred odd? throw-odd))
=> (throw-odds 2)
false
=> (throw-odds 3)
Execution error at user/throw-odd (REPL:1).
Odd: 3
````

What would be _really_ cool is to have an "every" function that returns 
a value like `some-fn`. Maybe call it `every-fn`?

## [complement][complement]

Our logical composers just wouldn't be complete without a logical `not`.
`complement` quite literally takes a function and wraps it in a `not` form.
Pretty straightforward.

Since our function is wrapped in a `not`, the result is always a boolean
value.

This is useful when you need both the positive and negative form 
of a predicate.

Here are a few examples of some equivalent forms.

````clojure
(def not-blank? (complement blank?))
(def not-blank? (comp not blank?))
(def not-blank? #(not (blank? %)))
(defn not-blank? [s] (not (blank? s)))
````

## [juxt][juxt]

`juxt` creates a function that places the result of each function you 
provide it into a vector.

````clojure
=> (def neighbors (juxt inc dec identity))
=> (neighbors 0)
[1 -1 0]
=> (neighbors 5)
[6 4 5]
````

It's effectively just `mapv`, but with functions over a value
rather than values over a function.

````clojure
=> (defn neighbors-mapv [n] (mapv #(% n) [inc dec identity]))
=> (= (neighbors-mapv 5) (neighbors 5))
true
````

This is particularly useful when you want to sort by multiple values.

````clojure
=> (sort-by (juxt :a :b) [{:a 4 :b 5} {:a 4 :b 2} {:a 3 :b 5}])
({:a 3, :b 5} {:a 4, :b 2} {:a 4, :b 5})
````

Keep in mind that order matters with `juxt`. The order of the functions is the order
in which their results will be placed into the vector.

## [comparator][comparator]

Last but not least, we have `comparator`. This function will take a 2-arity function
and return a new function that conforms to the [java.util.Comparator][java-comparator]
interface.

Basically, this function creates a function that returns 1, -1, or 0 based on a
predicate.

Suppose I want to sort some books by how heavy they are, but I can only _guess_
the weight by the type of book it is: `:hardcover`, `:softcover`, or `:ebook`.

We may already have a function that looks something like this, which returns either
a tuple (truthy) or `nil` (falsy).

````clojure
=> (defn heavier-than? [a b]
    (#{[:hardcover :softcover]
       [:hardcover :ebook]
       [:softcover :ebook]} 
     [a b]))
=> (heavier-than? :hardcover :hardcover)
nil
=> (heavier-than? :softcover :ebook)
[:softcover :ebook]
````

We can't `sort` or `sort-by` this function, because it doesn't directly conform to 
the compare interfaces required by those functions (i.e. we will get errors). 
However, `comparator` allows us to use to turn this function into a comparer!

````clojure
=> (def compare-weight (comparator heavier-than?))
=> (sort compare-weight [:hardcover :softcover :ebook :hardcover])
(:hardcover :hardcover :softcover :ebook)
````

Note that this function _may_ execute the compare function twice. So if comparing
two objects requires a lot of resources, or if you're comparing a very large collection,
it may be better to just create your own compare function.

## Testing Compositions

It's important to be aware of how composed functions behave when using them in 
the context of `with-redefs`.

Within the scope of `with-redefs`, composed functions are redefined just as you'd expect.

````clojure
=> (with-redefs [inc dec] 
     (let [plus-three (comp inc inc inc)]
       (plus-three 5)))
2
````

However, when defined outside the scope of `with-redefs`, composed functions
behave the way they were originally defined–`with-redefs` has no effect on the 
behavior of the function.

````clojure
=> (def plus-three (comp inc inc inc))
=> (with-redefs [inc dec] (plus-three 5))
8
````

## Clean Composition

These composers are meant to help keep your code clean and easy to read–they
should not make things more obscure. So when deciding between a composer or a
normal `fn` or `defn` or whatever, always choose the one that you find easiest 
to understand.

````clojure
(def add-ab-clean [m] (+ (:a m) (:b m)))
(def add-ab-meh (comp (partial reduce +) (juxt :a :b)))
````

## Conclusion

That's all, folks! At least, those are all the composers I could think of at this time.
If you're interested to see more examples, I have a [cljex][cljex] repository
with some examples for most (not all) of these function composers.


[constantly]: https://clojuredocs.org/clojure.core/constantly
[partial]: https://clojuredocs.org/clojure.core/partial
[fnil]: https://clojuredocs.org/clojure.core/fnil
[comp]: https://clojuredocs.org/clojure.core/comp
[some-fn]: https://clojuredocs.org/clojure.core/some-fn
[every-pred]: https://clojuredocs.org/clojure.core/every-pred
[complement]: https://clojuredocs.org/clojure.core/complement
[juxt]: https://clojuredocs.org/clojure.core/juxt
[comparator]: https://clojuredocs.org/clojure.core/comparator
[java-comparator]: https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html
[cljex]: https://github.com/brandoncorrea/cljex