---
layout: post
title:  "Ruby feels ... functional?"
date:   2021-12-23 7:52:05 -0400
categories: ruby apprenticeship
---

I recently started learning Ruby and going through the [koans][ruby-koans].
Maybe it's because I've been coding in Clojure recently, but Ruby feels a 
lot like a functional programming language.

First is Ruby has functions like reduce and map, which I don't see too often
in OO languages. So instead of using a for loop, you can map or reduce a collection.

````ruby
nums = [3, 4, 5]
nums.reduce(:+)
# 12
nums.reduce(:-)
# -6
nums.reduce(:*)
# 60
nums.map { |n| n * n }
# [9, 16, 25]
nums.map { |n| n + 1 }
# [4, 5, 6]
````

This has probably become one of my favorite features in any language. It just
makes everything so much easier, prettier, and you never really have to worry
about off-by-one errors or infinite loops (unless you have an infinite sequence).

Another interesting observation is that the evaluation of the last line in a 
function is returned from the function.

````ruby
def add(x, y)
    return x + y # Explicit Return
end
add(1, 2) # 3

def add(x, y)
    x + y # Implicit Return
end
add(1, 2) # 3
````

In Clojure, the last line of a function is returned as the result 
whereas in OO languages, you must specify some `return` keyword. It looks 
like Ruby decided to support both return operations, which is quite nice.

After doing a little Googling on what languages Ruby is based off of, 
its functional nature started to make more sense. On [ruby-lang.org][about-ruby],
it explains that the language was inspired by several languages, which include 
both Object-Oriented and Functional.

This explains why Ruby has this Functional flavor to it, while also supporting
many of the features you might see in an Object-Oriented language.

[ruby-koans]: https://github.com/edgecase/ruby_koans
[about-ruby]: https://www.ruby-lang.org/en/about/