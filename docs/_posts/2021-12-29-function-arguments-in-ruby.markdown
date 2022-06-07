---
layout: post
title:  "Function Arguments in Ruby"
date:   2021-12-29 7:52:05 -0400
categories: ruby, apprenticeship
---

Sometimes you just need to pass a function to another function, and Ruby provides a few 
different ways of doing this. Ruby's higher-order functions like map and reduce are examples
of functions that accept other functions as parameters. So what does this look like in Ruby?

One way to pass in a function parameter is to use its associated keyword. Here, 
the `+` operator is treated as a function, so we can pass `:+` in as a parameter.

````ruby
["Hello", "Mr.", "Anderson"].reduce(:+)
# HelloMr.Anderson
````

One drawback to this is that it will only work with functions that are 
natively supported by that type–but there are ways around this.
Another thing to be aware of is that different functions may require different syntax...

````ruby
["1", "2", "3"].map(:to_i)
# wrong number of arguments (given 1, expected 0) (ArgumentError)
["1", "2", "3"].map(&:to_i)
# [1, 2, 3]
````

...While other functions don't really seem to care what syntax you use.

````ruby
[1, 2, 3].reduce(:+)  # 6
[1, 2, 3].reduce(&:+) # 6
````

But what if we need something more specific, like selecting only numbers 
divisible by 3? Well Ruby provides block syntax for this.

````ruby
(1..9).filter {|i| i % 3 == 0}
# [3, 6, 9]
````

But I'd much rather write `filter :divisible_by_3?`, or something like that.
Well, we can get _something like that_ if we extract the method and use Ruby's 
`method` function.

````ruby
def divisible_by_3?(i)
    i % 3 == 0
end

(1..9).filter(&method(:divisible_by_3?))
# [3, 6, 9]
````

