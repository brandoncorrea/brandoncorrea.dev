---
layout: post
title:  "Stubbing Out Your Dev Environment"
date:   2022-03-14 7:52:05 -0400
categories: apprenticeship
---

In most apps there's usually a way for a user to sign in,
providing access to a number of additional features. However,
these features can be difficult to test if you're unable to
actually sign in.

In an app I'm currently working on, there are certain things
that are just difficult to see without either signing in or 
having access to some different user privileges. So how do
we get over these sorts of hurdles?

Well, one way is to just create users directly from the
database! This seems like an obvious first step, so I don't 
know why it took me so long to do this. Just by creating
a few dummy users with different variables, I'm able to 
take care of a lot of my user-based tasks.

Now, what about when you can't access any of the sign-in
endpoints? Maybe they're all external providers. I had this 
problem when I tried signing in from another machine on the
network, since `localhost` means something different from one
machine to another. There are several ways to approach this 
problem, but this is how I did it.

We have some routes that will only work in the localhost/dev
environment. With this, a route could be added that would
establish a dummy user without depending on a login provider.

These sorts of tricks aren't only for sign-in purposes. 
Most things that depends on something other than the 
application being tested should be able to be stubbed out 
for some dummy data in the development environment. However,
be careful not to let this code leek into public environments.
