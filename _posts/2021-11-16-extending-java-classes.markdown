---
layout: post
title:  "Extending Java Classes"
date:   2021-11-16 7:52:05 -0400
categories: java, apprenticeship
---

I thought I'd try something new out in Java today, relating to inheritance.

There are a couple sorting algorithms that _can_ be implemented in terms of 
the generic [List][list-interface], and I currently have these implemented 
in terms of a named List type. What I want is to be able to do something like
`genericList.bubbleSort()` in code.

### Extension Methods

In C#, extension methods are just static methods created for existing classes.
So if I wanted List to have a `BubbleSort` method, and cannot change its 
source code, then I would just create an extension method for it:

````cs
public static class ListExtensions {
    public static void BubbleSort<T>(this List<T> list, Func<T, T, int> comparer) {
        // Implementation
    }
}
````

Now in the rest of my code, I would just include the namespace that this 
extension method lives under to write something like this:

````cs
public void DoStuff() {
    List<Narwhal> narwhals;
    // ...
    narwhals.BubbleSort((a, b) => {
        if (a.TuskLength > b.TuskLength)
            return 1;
        else if (a.TuskLength < b.TuskLength)
            return -1;
        return 0;
    });
    // ...
}
````

### Extending Java Classes

After a bit of research on this, I don't see any easy way to do this in Java.
Project Lombok has a library with an [@ExtensionMethod][extension-method]
annotation, but I'd like to avoid importing libraries that I'd only use once.

Instead, there are a couple ways I've found to extend the List interface:
- Subclassing
- Static Methods

### Subclassing

This was probably the worst way to achieve my goal, but I'll include it 
anyways since it was a fun experiment.

By extending the List interface, a new interface could be created: 
SortableList. Here, I can take advantage of the List methods without
having to implement the entire interface (which would only redirect
to the base List).

In this new interface, I can declare and define default implementations 
of the sorting algorithms:

````java
public interface SortableList<T> extends List<T> {
    default void bubbleSort(BiFunction<T, T, Integer> comparer) {
        // Implementation
    }

    default void mergeSort(BiFunction<T, T, Integer> comparer) {
        // Implementation
    }
}
````

Now if I wanted to use this class on a Vector, for example, I would need
something like this somewhere:

````java
public class SortableVector<T> 
        extends Vector<T> 
        implements SortableList<T> { }
````

As you can probably tell, this has quite a few drawbacks. First, what's
going on with SortableList? It's an interface, but has default 
implementations for both of its methods. 

Also, who wants to declare a new class for every type of list, just to use
two methods?

Interfaces don't allow for private methods, so any duplicate code cannot
be extracted without either exposing it to clients or putting it in 
another helper class.

### Static Methods

Static methods are more than likely the route I will decide to go on.
First, I need to place static implementations in a class, which will accept
a List and comparer as parameters.

````java
public class Sorting {
    public static <T> void bubbleSort(List<T> list, BiFunction<T, T, Integer> comparer) {
        // Implementation
    }

    public static <T> void mergeSort(List<T> list, BiFunction<T, T, Integer> comparer) {
        // Implementation
    }
}
````

Now in my client code, I can import this Sorting class and use its methods
on any type of List.

````java
public void doStuff() {
    Vector<Integer> vectorList;
    ArrayList<Narwhal> arrayList;
    // ...
    Sorting.bubbleSort(vectorList, Integer::compare);
    Sorting.mergeSort(arrayList, (a, b) -> {
        if (a.tuskLength > b.tuskLength)
            return 1;
        else if (a.tuskLength < b.tuskLength)
            return -1;
        return 0;
    });
}
````

This implementation is much more lightweight. I can create private helper 
methods in the Sorting class and do not need to create additional classes
in order to utilize these functions.

[list-interface]: https://docs.oracle.com/javase/8/docs/api/java/util/List.html
[list-iterator]: https://docs.oracle.com/javase/8/docs/api/java/util/ListIterator.html
[extension-method]: https://projectlombok.org/features/experimental/ExtensionMethod