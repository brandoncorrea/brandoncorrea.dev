---
layout: post
title:  "A Short History of SRP"
date:   2021-10-11 7:52:05 -0400
categories: design principles
---

I was always bad at remembering names and dates in history class. But still,
it was interesting to get a glimpse of what things were like "back then" or
see how some scientific breakthrough was discovered.

While researching the SOLID principles, I wanted to go a bit deeper to see 
where these principles came from and how they've developed over the years.
What follows are quotes and summaries of some sources I found pertaining to 
The Single Responsibility Principle (SRP) and how it came to be.

I highly recommend reading through Conway's paper,
[How Do Committees Invent?][committees-paper]
It really is fascinating to see how it relates to software design today, even
over fifty years after being written.

## [Conway's Law][conways-law]
_"Any organization that designs a system (defined broadly) will produce a design 
whose structure is a copy of the organization’s communication structure."_

Melvin E. Conway

### [How Do Committees Invent?][committees-paper]

In 1968, Dr. Melvin E. Conway wrote a paper that answers the question: Is 
there any predictable relationship between the graph structure of a design 
organization and the graph structure of the system it designs? In his paper, 
he concluded with what we now know as Conway's Law (quoted above).

In the text, he describes how a system can be broken down into smaller 
subsystems, how these subsystems are delegated to different design groups, and
how the resulting structure of the system relates to the organization that
designed it.

### Example
A contract research organization had eight people who were to produce a COBOL 
and an ALGOL compiler. After some initial estimates of difficulty and time, 
five people were assigned to the COBOL job and three to the ALGOL job. The 
resulting COBOL compiler ran in five phases, the ALGOL compiler ran in three.
Two military services were directed by their Commander-in-Chief to develop 
a common weapon system to meet their respective needs. After great effort 
they produced a copy of their organization chart.

## [Cohesion][cohesion-wiki]
Tom DeMarco and Meilir Page-Jones described SRP as cohesion. Unfortunately
I do not have their books, but have included links for those interested.

In the book, [Agile Principles, Patterns, and Practices in C#][agile-ppp], 
cohesion is described as the functional relatedness of the elements of a module.
This description is then modified as _the forces that cause a module, or a 
class, to change._

[Structured Analysis and System Specification, Tom DeMarco][structured-analysis]

[The Practical Guide to Structured Systems Design, Meilir Page-Jones][structured-systems]

## An Active Corollary

_"The best structure for a software system is heavily influenced by the social
structure of the organization that uses it so that each software module has one,
and only one, reason to change."_

Robert C. Martin, "Uncle Bob"

In his book, [Clean Architecture][clean-architecture], Robert C. Martin 
summarizes SRP as "An active corollary to Conway's law." Later in the book, 
he revises SRP a couple times from its original description:
1. _A module should have one, and only one, reason to change._
2. _A module should be responsible to one, and only one, user or stakeholder._
3. _A module should be responsible to one, and only one, actor._

It's interesting to see how these revisions relate to one another. Each wording
says the same thing, but can be understood differently by different people.

## [Allocation of Responsibility][allocation-of-responsibility]
_"...Your software is responsible for serving its actors and protecting the 
interests of its stakeholders."_

Randy Stafford

This isn't directly related to SRP, but I found it interesting to see how this
relates to other descriptions of the principle. The rest of the text also 
seems to encourage the use of the design principles.

## Conclusion

The Single Responsibility Principle can be defined in many ways, but with each 
interpretation of this principle, a module relates to some entity or actor. 
The dependency of any given module must point to a single actor; the entire 
system must point toward all actors.

This even seems to echo the first value in the 
[Agile Manifesto][agile-manifesto], favoring the people involved:

_Individuals and interactions over processes and tools._

[conways-law]: http://www.melconway.com/Home/Conways_Law.html
[committees-paper]: http://www.melconway.com/Home/pdf/committees.pdf
[cohesion-wiki]: https://en.wikipedia.org/wiki/Cohesion_(computer_science)
[structured-analysis]: https://www.amazon.com/Structured-Analysis-System-Specification-DeMarco/dp/0138543801
[structured-systems]: https://www.amazon.com/Practical-Structured-Systems-Computing-Page-Jones/dp/B00DO91IR8
[clean-architecture]: https://www.amazon.com/Clean-Architecture-Craftsmans-Software-Structure/dp/0134494164/ref=pd_lpo_3?pd_rd_i=0134494164&psc=1
[agile-ppp]: https://www.amazon.com/Agile-Principles-Patterns-Practices-C/dp/0131857258
[c2-srp]: https://wiki.c2.com/?SingleResponsibilityPrinciple
[allocation-of-responsibility]: http://wiki.c2.com/?AllocationOfResponsibility
[agile-manifesto]: https://agilemanifesto.org
