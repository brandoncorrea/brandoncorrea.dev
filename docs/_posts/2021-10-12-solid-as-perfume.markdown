---
layout: post
title:  "SOLID as Perfume"
date:   2021-10-12 7:52:05 -0400
categories: principles, apprenticeship
---

Have you ever been out somewhere and all of a sudden started choking on your own
breath because someone decided to wear WAY too much cologne or perfume? It's 
not a very pleasant experience, but at least the experience is short-lived.
Unfortunately bad experiences in software development are not generally
short-lived, but can linger for weeks, months, or even years.

### Choking on SOLID

As described in 
[Agile Principles, Patterns, and Practices in C#][agile-ppp],
_"The principles are there to help us eliminate bad smells. They are not a 
perfume to be liberally scattered all over the system."_

I really like the relationship between SOLID and perfume. SOLID looks great,
smells great, covers up design smells very well, but can become a smell itself 
if overused. If there is no need to apply a principle, then it should not be
applied until the need for it arises. Doing so prematurely would result in
the design smell of needless complexity.

### When to Spray

Okay, so you might be thinking, "What does this mean? When should I apply the
principles?" Well I'm glad you asked!

Suppose you're working on an application to calculate your odds of world 
domination. You need some database methods and models to represent that data 
in code, so you separate this logic out into different classes, but leave them 
all in the same module. It's early on in the project, and you don't expect to 
make any changes. The final application is then exported as a single binary file.

A few months later, you get an email from the Prince of Nigeria with a feature
request. He is willing to pay you a large sum of money for the seemingly minor 
updates, so of course you accept the offer and get to coding. You know you're 
going to need those models you created earlier as well as the database logic 
to store and retrieve those models.

At this point, it seems reasonable to apply some SOLID Principles. You separate 
your database logic into a data store module and your place your models into a 
core or common module. You also decide to include some abstractions related to 
the changes, because it's likely that the Prince will be requesting more 
of those kinds of changes.

### Consequences of Premature Application

In the case above, if the principles had been applied prematurely, how would we
know where to apply them? We would likely end up with an application that
contains abstractions that are never used, or libraries that are only used by
a single module. If no new changes were ever requested, then we would be 
carrying around extra binaries for no reason!

Applying the principles without the presence of "design smells" adds complexity
that only gets in the way of future development. It becomes a smell of its own; 
the Axe Body Spray of cologne; the Vienna Sausage of meat.

[agile-ppp]: https://www.amazon.com/Agile-Principles-Patterns-Practices-C/dp/0131857258
