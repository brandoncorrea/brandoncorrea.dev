---
layout: post
title:  "Simpler is Better"
date:   2022-03-02 7:52:05 -0400
categories: clojure apprenticeship
---

[Occam's Razor][occam] is a principle that favors simpler solutions or 
explanations over more complex ones. So for instance, let's say I'm faced 
with two explanations on why I keep losing one of a pair of socks:
1. One gets lost in the dryer
2. Sock Gnomes sneak into the house and steal only one sock from the pair

The simpler (and more likely) explanation is that it gets lost in the dryer.

In TDD, we use this principle with every step!
1. Write the minimum amount of code to fail
2. Write the minimum amount of code to pass
3. Refactor

Typically, the minimum amount of code will result in the simplest solution
for the tst suite. Then in the refactor step, we try to find ways to make
the code even simpler!

Let's take a look at the first test for the [Bowling Game Kata][bowling].
If you're not familiar with it, that's fine. The point is in the process.

We start by writing the minimum amount of code to fail.

````clojure
(it "rolls a gutter game"
  (should= 0 (score (repeat 0))))
````

This fails because `score` is not defined, so we stop testing and begin 
writing the function we are creating.

````clojure
(defn score [_] 0)
````

This now satisfies our test, and we can continue with the 
red-green-refactor cycle until we have the code we need.

Now aside from the fact that there aren't any other tests, why wouldn't
we want to write any more than just `0` in our function? Why not guard 
against `score` receiving non-sequential values, or even give our
parameter a name?

We don't write anything more than this because this is as simple as we 
can possibly get with this function. Anything else would just produce
needless complexity to our function. This function returns zero no matter
what! We don't need to travel through additional logic structures, functions,
or files to figure that out.

We don't care what we pass into the function because the function itself 
doesn't care what's passed into it, except that it has one parameter.

Now take this a few levels higher. When building out an application, do you
*need* an interface for every class? Do you need additional helper functions?
I must say, I enjoy deleting code almost as much as writing it, whether I put it
there or not. How much simpler would your codebase be if you removed everything
that wasn't necessary to the application?

[occam]: https://en.wikipedia.org/wiki/Occam%27s_razor
[bowling]: http://butunclebob.com/ArticleS.UncleBob.TheBowlingGameKata