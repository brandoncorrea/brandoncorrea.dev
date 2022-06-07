---
layout: post
title:  "A Glimpse of Java"
date:   2021-10-14 7:52:05 -0400
categories: java, apprenticeship
---

I've only ever used Java twice prior to today. The first time was shortly
after a C++ course where I did a couple _very_ small exercise problems. The
second time was a couple years later in a data structures course for some
homework assignments, and I think that was mainly some fill-in-the-blank stuff.

So needless to say, actually get to the coding part took a bit to figure out.

### The First Test

After I figured out the IDE situation and how to set that up, I had to write a 
test... _Googles: What does a test look like in Java?_

Ah! So what we want to do is create a .java file, something like 
~/src/className/ClassNameTest.java. I don't actually know the naming convention, 
but this is how it looked on Google, so I'm using it here. We'll also want 
JUnit, so lets import that as well.

````java
package some.base.pkg.sassyClassy;

import org.junit.Assert;
import org.junit.Test;

public class SassyClassyTest {
    // Test Code...
}
````

The most basic functionality of a class is instantiation, so we'll test that first.

````java
@Test
public void newSassyClassy() {
    SassyClassy sassy = new SassyClassy();
}
````

Now that we have a failing test, we can create our SassyClassy code in the same 
directory. ~/src/sassyClassy/SassyClassy.java.

````java
package some.base.pkg.sassyClassy;

public class SassyClassy {
    
}
````

From here we can start testing and building out the functionality for our class.

### A Vague Familiarity

My background is in C#, which I keep hearing is "basically Java," but I never 
really saw it until today. It did come from J# after all, which came from Java, 
so it makes sense that they'd be similar. The syntax is almost the same, with 
only a few negligible differences.

Probably the biggest difference between the two is naming conventions and the 
core libraries they come with, which are expected to change as with any language.
So really not much of a difference between the two.

The naming convention in Java seems to be PascalCase for classes and camelCase 
for everything else. This is a pretty normal convention for the majority of 
languages. C# is quite the opposite, with everything being PascalCase, except 
local variables.

If you're also coming from a C# background, Java shouldn't look completely 
foreign. It's a little like visiting another city – you can read all the signs
and communicate with people, but you don't know where to find a good coffee shop.
