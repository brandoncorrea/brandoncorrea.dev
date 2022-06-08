---
layout: post
title:  "Coin Changer Kata"
date:   2021-10-24 7:52:05 -0400
categories: clojure, apprenticeship
---

I've been working on the Coin Changer Kata this past week. Below is a sort
of written thought process of how I approached the exercise.

### Defining the Interface

The first test defines the interface that will be used for this function; the 
name, the parameters, and the return value. I ended up going with a map as 
the return value, since we can assign key-value pairs for each coin
denomination.

I initially used the name, `gimme-some-change`, because I wasn't quite sure 
what I wanted to name it at the time. I ended up going with `exchange` as 
this seemed to get right to the point. However, if our library were to 
exchange coins both ways, then this name may be too ambiguous.

For this test, I just want to make sure I get no change back for $0.00.

````clojure
(it "Zero dollars results in no change"
  (should= {} (exchange 0.00)))
````

To pass this test, we can simply define the function and return an empty map.

````clojure
(defn exchange [_] {})
````

### Exchanging a Penny

Going with the next smallest step, the second test will exchange $0.01.

````clojure
(it "One cent results in one penny"
  (should= {1 1} (exchange 0.01)))
````

You may be wondering why I used `{1 1}` instead of `{:pennies 1}`. I used the 
coin denomination as the key instead of the coin name because I like to avoid 
using words in code if I can help it. It's too easy to accidentally type things
like `:penny` and `:pennys`, or `:nickles` versus `:nickels`.

To pass this test, we just need an if statement with the expected values.

````clojure
(defn exchange [dollars]
  (if (zero? dollars)
    {}
    {1 1}))
````

Again I used a dummy name, `moneys`, in my initial versions of this kata.
I finally ended up with `dollars` as it seemed to best suit the use case.
That _is_ what we are exchanging after all–dollar values for change.

The `$` symbol also came to mind, as this happens to work fine as a parameter.
My only problem with this is it makes things hard to read, even with a small
function body.

````clojure
(defn exchange [$]
  (if (zero? $)
    {}
    {1 1}))
````

Using this symbol would surely stall the reader, "What does that dollar sign 
mean? Why did the author decide to use that instead of a word? Should I read 
this as 'zero dollar sign'?" Blah! Just use the word, dollar.

### Two Cents

Looking at the code, we can see that this will only ever work with $0.00 and 
$0.01. But never for any other value. So let's test $0.02.

````clojure
(it "Two cents results in two pennies"
  (should= {1 2} (exchange 0.02)))
````

The penny count is equal to our `dollars` times 100, so let's just set 
it to that value (cast to an integer).

````clojure
(defn exchange [dollars]
  (if (zero? dollars)
    {}
    {1 (int (* 100 dollars))}))
````

### Nickels Ruin Everything

Now that we have our penny implementation, this function will break when we
get to $0.05, so let's write a test for that.

````clojure
(it "Five cents results in one nickel"
  (should= {5 1} (exchange 0.05)))
````

Easy peasy. We'll just get the nickel quotient of our cents and change our 
penny implementation to be the remainder of the cents divided by 5.

````clojure
(defn exchange [dollars]
  (if (zero? dollars)
    {}
    {1 (rem (int (* 100 dollars)) 5)
     5 (quot (int (* 100 dollars)) 5)}))
````

This still fails though! Our test is expecting `{5 1}`, but we are getting
`{1 0, 5 1}` instead. We will need to `filter` out anything with a 0 value
and put the results `into` a new map.

````clojure
(defn exchange [dollars]
  (if (zero? dollars)
    {}
    (into {} (filter #(not= 0 (second %))
                     {1 (rem (int (* 100 dollars)) 5)
                      5 (quot (int (* 100 dollars)) 5)}))))
````

Yay, it works! But this function is starting to look ugly... What on earth is
going on here?! Let's do some refactoring.

### Cleaning Up the Mess

First we can get rid of the conditional, since we're filtering out zero values.

````clojure
(defn exchange [dollars]
  (into {} (filter #(not= 0 (second %))
                   {1 (rem (int (* 100 dollars)) 5)
                    5 (quot (int (* 100 dollars)) 5)})))
````

Next I want to move that `filter` `into` stuff to another function. 
That's basically making our output look pretty–let's call that `beautify`! 
We'll also want to do something about that anonymous function.

````clojure
(defn- has-coins? [[_ coin-count]] (not= 0 coin-count))
(defn- beautify [change] (into {} (filter has-coins? change)))

(defn exchange [dollars]
  (beautify {1 (rem (int (* 100 dollars)) 5)
             5 (quot (int (* 100 dollars)) 5)}))
````

Almost there! We still need to move that dollar conversion logic out and 
move the map to another function. That dollar conversion is just converting
"dollars to cents," and the map is converting our "cents to change."

````clojure
(defn- ->cents [dollars] (int (* 100 dollars)))
(defn- ->change [cents]
  {1 (rem cents 5)
   5 (quot cents 5)})

(defn exchange [dollars]
  (-> dollars ->cents ->change beautify))
````

Now our highest level function, `exchange`, reads "Dollars, to cents, 
to change, beautified!"

### Dimes Are Easy

Our next test will give us our dime implementation.

````clojure
(it "Ten cents results in one dime"
  (should= {10 1} (exchange 0.10)))
````

This is easy. We just need to get the quotient of the cents for dimes, 
just like we did for the nickels. 

We'll also need to divide the quotient of nickels by two and set nickels 
to the remainder of that, since we should never receive more than one 
nickel for any dollar amount.

````clojure
(defn- ->change [cents]
  {1  (rem cents 5)
   5  (rem (quot cents 5) 2)
   10 (quot cents 10)})
````

This passes our tests. I don't see any immediate need for refactoring at this
point, so we'll move on to quarters.

### Quarters Are Also Easy

Our quarters test will look just about the same as our nickel and dime tests.

````clojure
(it "Twenty-five cents results in one quarter"
  (should= {25 1} (exchange 0.25)))
````

You may start to notice a pattern here. We'll be getting the quotient of
the cents for quarters, then wrapping `cents` with the remainder of the
quarters' quotient.

````clojure
(defn- ->change [cents]
  {1  (rem cents 5)
   5  (rem (quot (rem cents 25) 5) 2)
   10 (quot (rem cents 25) 10)
   25 (quot cents 25)})
````

I think one thing we could refactor here is our nickel implementation.
This makes it clear how each coin relates to the next.

````clojure
(defn- ->change [cents]
  {1  (rem cents 5)
   5  (quot (rem (rem cents 25) 10) 5)
   10 (quot (rem cents 25) 10)
   25 (quot cents 25)})
````

### Done ...!?

While we haven't written our final test yet, the current `->change` function 
will _technically_ work for any dollar amount. But do we want to refactor 
this any further? We really only need this to work for the four coins, 
and there's only one snippet of duplication here.

### Loop Implementation

Just for fun I'd like to see how this would look in a loop.

After more naming struggles, I wound up with `+coin` for my `change` 
recurse/helper function. This made the most sense, since it's just adding a 
coin value to the `change` map.

````clojure
(defn- +coin [change coin cents]
  (assoc change coin (quot cents coin)))

(defn- ->change [cents]
  (loop [cents cents
         change {}
         [coin & rest-coins] [25 10 5 1]]
    (if coin
      (recur (rem cents coin) (+coin change coin cents) rest-coins)
      change)))
````

This is significantly more code, and would probably result in more overall
calculations, but this makes it much easier to add new coin denominations 
for half-dollars or full dollar coins. Indeed, removing coin denominations 
would be just as easy.

With this implementation, we also don't have random numbers floating around 
the function; all our numbers are in one place, and are named after what they 
represent: coins.

### Reduce Implementation

After some Googling and toying around with `reduce`, I finally got it to 
work with `->change`, but I'm not sure how I feel about it.

This is smaller than both of the other implementations, and still makes it 
easy to add and remove coin denominations. However, this implementation is 
hard to understand and both of these functions are tightly coupled; it'd be 
hard to find a use for `+coin` outside of this `reduce` function.

Also, what on earth is `second` doing there? We'd really have to analyze 
these two functions to realize that this is grabbing the `change` map we built.

````clojure
(defn- +coin [[cents change] coin]
  [(rem cents coin) (assoc change coin (quot cents coin))])

(defn- ->change [cents]
  (second (reduce +coin [cents {}] [25 10 5 1])))
````

### The Final Test

Just for good measure, I'd like to test out a random dollar amount to ensure
we get the correct amount of change.

````clojure
(it "$1.44 results in 5 quarters, 1 dime, 1 nickel, and 4 pennies"
  (should= {1 4 5 1 10 1 25 5} (exchange 1.44)))
````

And that's it! Our tests pass and our coin changer is ready for release.

### Test Code

````clojure
(ns coin-changer.core-spec
  (:require [speclj.core :refer :all]
            [coin-changer.core :refer :all]))

(describe "Coin Changer"
  (describe "exchange"
    (it "Zero dollars results in no change"
      (should= {} (exchange 0.00)))
    (it "One cent results in one penny"
      (should= {1 1} (exchange 0.01)))
    (it "Two cents results in two pennies"
      (should= {1 2} (exchange 0.02)))
    (it "Five cents results in one nickel"
      (should= {5 1} (exchange 0.05)))
    (it "Ten cents results in one dime"
      (should= {10 1} (exchange 0.10)))
    (it "Twenty-five cents results in one quarter"
      (should= {25 1} (exchange 0.25)))
    (it "$1.44 results in 5 quarters, 1 dime, 1 nickel, and 4 pennies"
      (should= {1 4 5 1 10 1 25 5} (exchange 1.44)))))
````

### Source Code

I decided to go with the `loop` version of `->change` because it had the
best balance of conciseness and maintainability compared to the reduce and 
raw map implementations.

````clojure
(ns coin-changer.core)

(defn- has-coins? [[_ coin-count]] (not= 0 coin-count))
(defn- beautify [change] (into {} (filter has-coins? change)))
(defn- ->cents [dollars] (int (* 100 dollars)))

(defn- +coin [change coin cents]
  (assoc change coin (quot cents coin)))

(defn- ->change [cents]
  (loop [cents cents
         change {} 
         [coin & rest-coins] [25 10 5 1]]
    (if coin
      (recur (rem cents coin) (+coin change coin cents) rest-coins)
      change)))

(defn exchange [dollars]
  (-> dollars ->cents ->change beautify))
````