---
layout: post
title:  "Abstract Factory Pattern"
date:   2021-11-04 7:52:05 -0400
categories: patterns apprenticeship
---

Today I began researching a design pattern, Abstract Factory! I feel like
one of the SOLID principles is really stressed in this pattern, the Liskov
Substitution Principle.

Because this pattern focuses on abstractions, and creating instances of 
abstractions, it is even more important that all promises made by these 
abstractions are kept by their implementations.

### AKA, The Kit Pattern

This pattern is meant to provide a (factory) interface for clients to 
instantiate concrete implementations, while remaining unaware of any named 
class.

There are two key benefits to this design pattern:
- Clients commit to an interface, rather than a concrete class.
  - Interfaces tend to be more stable.
  - Interface implementations tend to be more easily replaceable.
- It encapsulates classes that were intended to be coupled together.
  - This prevents a class designed for feature A from interacting with a 
  class designed for feature B.
  - If we want this interaction to occur, we should create a new factory
  for feature C that allows classes from features A and B to interact.
  - We do not have to manage this separation in the client code–it is done
  naturally through the factories.

There are four things that must be done to use this pattern:
- Create an interface for each _type_ of concrete class to be instantiated 
by the factory.
- Implement the concrete classes in terms of their associated interface.
- Create an interface for the factory which defines methods to instantiate
the interfaces you created for each type of class.
- Implement your concrete factory in terms of the factory interface you created.

Your client code can instantiate concretions using the abstract factory, 
and can then interact with them using the interfaces defined for those 
concrete classes.

Again, the client knows only of abstractions and is oblivious to 
implementations. There should be only one place where a concretion is named, 
which is somewhere in the entry point of the application (`main`, for example).

### _Aaah! Zombies!!_

Suppose you're making a game where a player must fight various monsters:
minor, normal, and boss. Your users like the idea of fighting zombies, but 
would prefer a robot version for their kids to play.

The Abstract Factory pattern could be used here to create a `ZombieFactory` and 
`RobotFactory`, each implementing an abstract interface, `MonsterFactory`.

We would start by creating our abstract Monster classes to represent the various
monsters. These would just contain the methods the client application needs to
know about to use the class.
- `BossMonster`
- `NormalMonster`
- `MinorMonster`

After your abstract monster classes have been created, your concrete monster
classes will need to implement these interfaces.

Next, the abstract factory class would need to be created with methods to
instantiate the different types monsters.

````java
class MonsterFactory {
  MinorMonster CreateMinorMonster(int level);
  NormalMonster CreateNormalMonster(int level);
  BossMonster CreateBossMonster(int level);
}
````

And finally, the concrete factory implementations...

````java
class ZombieFactory {
  MinorMonster CreateMinorMonster(int level) {
    return new LazyZombie(level);
  }

  NormalMonster CreateNormalMonster(int level) {
    return new HangryZombie(level);
  }
  
  BossMonster CreateBossMonster(int level) {
    return new RobZombie(level);
  }
}
````

````java
class RobotFactory {
  MinorMonster CreateMinorMonster(int level) {
    return new Microwave(level);
  }

  NormalMonster CreateNormalMonster(int level) {
    return new HKDroid(level);
  }
  
  BossMonster CreateBossMonster(int level) {
    return new T800(level);
  }
}
````

Now that we have all of our concrete and abstract classes, we can instantiate 
a factory in our main function and use it anywhere in the application.

````java
class Program {
  void Main() {
    // ...
    MonsterFactory factory;
    if (zombieMode)
      factory = new ZombieFactory();
    else
      factory = new RobotFactory();
    // ...
    EasyMonster easyMonster = factory.CreateEasyMonster(1);
    MediumMonster mediumMonster = factory.CreateMediumMonster(3);
    BossMonster boss = factory.CreateBossMonster(5);
    // ...
  }
}
````

In our client application, the only concretion is the initial instantiation
of the monster factory. The rest of the code only knows about abstractions. 
If we wanted to re-skin the game, we could easily do so simply by creating a 
new monster factory.