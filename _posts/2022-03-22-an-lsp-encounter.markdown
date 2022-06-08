---
layout: post
title:  "An LSP Encounter"
date:   2022-03-22 7:52:05 -0400
categories: apprenticeship
---

Today I was reworking some entity structures and refactoring parts of
the code around these entities. Many of these entities were effectively
the same, but their interfaces had some slight differences, making it
difficult to reuse code across these different entities.

This resulted in some duplicated functionality of sorts. The functions for 
these entities were different in a number of minor ways, but the end result 
was more or less the same.

This was a pain to work with. Any time a feature needed to be added, it would
have to be created initially, then copied and modified for each kind of entity.
What a pain! In addition to this, any time a change was made to an existing 
feature, it would have to be changed in all the features. Then problems would
arise here and there because something would inevitably get missed in the process.

All these problems came about because of a violation of the 
[Liskov Substitution Principle][lsp]. The interface for the same property was
different among multiple entities, so nothing was substitutable for another.

While working on a bug fix, I came to a point where it seemed easier to make the
jump and normalize all of these entities to a common interface. It wasn't until
the refactoring phase that I realized how big of a different this would make.

As I was moving, renaming, and reorganizing code, I realized that the rules for
one entity are almost the same for the rest. Some functions just needed to be 
made a little more generic while others fit together with the new entities as-is!

Slowly I started to see a lot of the duplicate code leave the system. Some
modules just completely disappeared because so much of the code was now being 
shared among these now normalized entities!

Through this experience, though I'd rather not have to do it again, I could see
the power of following [LSP][lsp] as well as how bad things can get by violating it.

[lsp]: https://en.wikipedia.org/wiki/Liskov_substitution_principle
