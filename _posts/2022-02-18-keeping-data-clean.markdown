---
layout: post
title:  "Keeping Data Clean"
date:   2022-02-18 7:52:05 -0400
categories: clojure apprenticeship
---

Data management can be complicated sometimes, especially when you're 
working on a project where the entities are still being developed. 
But how can we ensure that our data doesn't get out of hand?

There are a couple of things we _don't_ want to happen with our data:
Unused properties and duplicated properties

Let's take a closer look at these.

### Unused Properties

Having unused properties is similar to having [dead code][dead-code], 
but worse. This is data that exists on a data model and all its
entities, but nowhere in the code is this property used.

While the first thing you may notice about these entities is that
the unused properties just get in the way, this is the least of your
problems. These unused properties can fester in the entities and one
day down the road cause unexpected problems in your application.

At some point, somebody will need to go through and remove these
unused properties from each entity.

### Duplicated Properties

Like unused properties, duplicated properties can get in the way
and distract you from what's actually going on. In addition to that,
any time you want to update an entity, you have to update it in two 
places just to keep consistency!

Suppose you have an entity that looks like this:

````clojure
{:full-name "Harison Ford"
 :first-name "Harison"
 :last-name "Ford"}
````

What happens when you need to update either the first or last name?
well, you'll need to update that property _and_ `:full-name`. Awful!

Now what if you, a developer, see this and need to update the name
somewhere in code? Well, you may not be fully aware that there is a 
`:first-name` and `:last-name` that need to be updated too. So you
just update `:full-name`. 

Now when Harrison goes back to fix his name, the entity looks like
this. `:full-name` was updated, but `:first-name` is still not right!

````clojure
{:full-name "Harrison Ford"
 :first-name "Harison"
 :last-name "Ford"}
````

Now the data is corrupt–there may be parts of the code that use all
three of these attributes which will no longer behave right when 
given this entity.

Just as it's important to keep your code clean, it is equally
important to keep your data clean.

[dead-code]: /apprenticeship/2022/02/04/death-to-dead-code.html
