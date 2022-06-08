---
layout: post
title:  "Preemptive Optimization"
date:   2022-01-25 7:52:05 -0400
categories: craftsmanship, apprenticeship
---

In the past, I would structure my code in such a way that it would execute in the least
amount of time using the least amount of resources. I later realized that this was an 
awful idea.

Most of the time, the optimizations were unnecessary to begin with. The amount of time 
and resources saved by those minor optimizations were negligible–maybe a few bytes or 
milliseconds here and there, but that's all.

One of the costs incurred by this was development time spent looking for and 
implementing these optimizations. The other (more damaging) cost was messy code.
Irrelevant coupling between modules, mixture of high-level and low-level code, and
other senseless decisions made for the sake of my preemptive optimizations.

### When should I optimize?

There will be times when you _need_ to optimize code. Maybe you notice things taking a 
little longer than they should, or maybe a customer informed you that their system 
crashes five minutes after opening your application. 

These are good reasons to optimize, but only with meaningful optimizations. 
Changing a data type from a `long` to a `short` probably won't make much of a 
difference. But reducing the amount of API calls or refactoring an algorithm will have 
a significant impact on performance.

### When should I NOT optimize?

If I must make an optimization, it is usually done in the refactor phase after all 
my tests are passing. Otherwise, unless the optimization is bocking, try to avoid 
optimizing before you get the green light.

When an optimization introduces a bug or causes a test to fail, then decide against it.

I was in a meeting a few years back discussing a bug with an API. The fix was just to
add a string format on one of the request parameters. At some point during the 
discussion, someone argued against the fix saying it would increase the amount of time
to process the request. While this was true, the amount of time added would be negligible. 

Optimizations aren't bad, but they will often make us do strange things for those extra 
bits of memory. So until your code proves otherwise, consider every machine that will
run your code to be a supercomputer until one proves otherwise.
