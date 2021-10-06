---
layout: post
title:  "Decorator Pattern"
date:   2021-10-06 08:11:22 -0400
categories: design patterns
---

The Decorator Pattern, or Wrapper Pattern, is a design pattern that 
allows you to add responsibilities to an object. There are four key 
players in the structure of this pattern:

- The Abstract Component
  - A core interface where all other classes inherit from.
- The Concrete Component(s)
  - An implementation of the Abstract Component
- The Decorator
  - Inherits from the Abstract Component
  - Holds a reference to an Abstract Component object
- The Concrete Decorator(s)
  - Inherits from the Decorator
  - Adds layer of responsibilities to an Abstract Component object
  
### Implementation
Let's say in our program, we want to have a Human and Dog. Let's say that 
we also want to be able to "decorate" them with a hat and boots. When they
put the boots on, they end every sentence with "Yee" and when they put the hat
on, they end every sentence with "Haw". If they have both, then both "Yee" and 
"Haw" are spoken.

First we would want to create an interface, Person, and two classes that 
inherit from this interface, Human and Dog.

````
interface Person
{
    Speak(message);
}

class Human : Person
{
    Human(name) => this.name = name;
    Speak(message) => print(this.name + ": " + message);
}

class Dog : Person
{
    Dog(name) => this.name = name;
    Speak(message) => print(this.name + ": Woof! " + message);
}
````

Now that we have our core interface, we can create our decorators.

````
abstract class PersonDecorator : Person
{
    PersonDecorator(person);
    Speak(message);
}

class BootDecorator : PersonDecorator
{
    BootDecorator(person) =>
        this.personComponent = person;
    Speak(message) =>
        this.personComponent.Speak(message + " Yee");
}

class HatDecorator : PersonDecorator
{
    HatDecorator(person) =>
        this.personComponent = person;
    Speak(message) =>
        this.personComponent.Speak(message + " Haw");
}
````

Now in main, we can build our Person objects and decorate them with a hat and boots!
````
function Main()
{
    bucky = HatDecorator(BootDecorator(new Human("Bucky")));
    lucky = BootDecorator(HatDecorator(new Dog("Lucky")));
    bucky.Speak("Look at that there snake!");
    lucky.Speak("I'm not gettin' near that thing..");
}
````

If you try to run this, you'd probably get an error since this isn't based on 
an actual language. But if you implemented this in a real programming language,
you should see output similar to this.
````
> Bucky: Look at that there snake! Haw Yee
> Lucky: Woof! I'm not gettin' near that thing.. Yee Haw
````

### Potential issues
One possible issue that can arise from the decorator pattern is adding 
decorators that can break other decorators. For instance, if we tried to 
add a sock decorator after we've added the boot decorator, Bucky and Lucky 
would just look silly walking around like that.

Another potential issue is having lots of small classes that differ in very 
small ways, which may make the system difficult to manage. However, I'd like
to think that there is a way to design a system that would take full advantage
of the many small classes that the pattern can produce.

### Testability
From a high-level perspective, it would seem as though this pattern would be as
easy to test as the feature itself. There may be some other caveats that I'm 
not taking into account, though I can't see this being more difficult than
testing the feature in a concrete implementation.

My initial approach would be to create an empty or default implementation 
of the core component, then pass an instance of that into the constructor 
of the decorator being tested. Now that we have a sort of "clean slate" to
work with, we can test the decorator without the overhead associated with
a concrete implementation.


### Final Thoughts
All in all, this seems like a very useful design pattern, if utilized properly.
It takes care of a lot of the work when it comes to feature combinations 
(A, AB, BA, etc) and seems like it would be easily testable. 

I'd like to see how this pattern would be implemented in a functional language.
My first thought would be something like this, but I'm sure there are other
ways to achieve this as well.

````clojure
(hat-decorator (boot-decorator (dog "Lucky")))
> {:name "Lucky" :speak (fn [message] ,,,)}
````
