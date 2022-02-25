---
layout: post
title:  "Your Tests Are Your Documentation"
date:   2022-02-24 7:52:05 -0400
categories: apprenticeship
---

Normally when I'm trying to figure out how a function works, I go to the
source code and try to work out "what if" scenarios either in my head or
on paper. Sometimes this doesn't work, so I have to Google or ask somebody 
for help.

Well, today I needed to know if [c3kit][bucket]'s `find-by` function could 
take multiple arguments for "or" queries. I ended up asking a coworker for 
help, to which he pointed me to one of the [tests][db-spec] demonstrating 
this behavior.

This was exactly what I needed! Not only does it answer my initial question,
"can this do that?", but it provides examples and demonstrates behavior as well.
I was even able to browse through this test document and see what else bucket 
could do. Who knew you could pass in `'not`, `'>`, or `'<` arguments?

What do your tests look like? Many testing frameworks use works like "describe" 
to open a testing context. Do your tests really describe your code to the point
where it could be read as documentation? Could somebody who is using your code 
figure out everything on their own just by referencing your test?

Tests are more than just nets to catch us when refactoring or making changes.
Tests tell us *what* the code does and *how* it works. If the tests do a poor
job at "describing" behavior, then the tests aren't being utilized to their full
potential.

[bucket]: https://github.com/cleancoders/c3kit/tree/main/bucket
[db-spec]: https://github.com/cleancoders/c3kit/blob/52d4986751ef2180775280c8f891c2499bdf530e/bucket/spec/cljc/c3kit/bucket/dbc_spec.cljc#L184
