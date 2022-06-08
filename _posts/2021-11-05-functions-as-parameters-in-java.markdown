---
layout: post
title:  "Functions as Parameters in Java"
date:   2021-11-05 7:52:05 -0400
categories: java, apprenticeship
---

Today I implemented a sort method in Java using the Bubble Sort algorithm! 
One problem I hadn't anticipated was having to implement it on a generic list.
This means I can't just compare values with `>` or `<` operators; I needed
a specialized comparer for each _type_ of value.

### Comparer Functions

Consider a comparer function, `compare(a, b)`, where `a` and `b` are some 
arbitrary values of arbitrary types. Common practice for these kinds of 
functions will define outputs like so:
- -1 if `a` is less than `b`
- 0 if `a` equals `b`
- 1 if `a` is greater than `b`

This means that the following function calls would produce these outputs:
- `compare(1, 2) => -1`
- `compare([1 2 3], [1 2 3]) => 0`
- `compare("B", "A") => 1`

### "Genericizing" Bubble Sort

So if the client wants to use this Bubble Sort function, they would need
to supply a "comparer" function–a function as a parameter.

Coming from the .NET world, I might create a function signature that looks
something like this:

````cs
public class GenericList<T> {
    public void BubbleSort(Func<T, T, int> comparer) {
        // ... Some sorting code ...
    }
}
````

The first two type parameters of `Func`, `T`, represent the first and second 
input types of `comparer`. The last type parameter, `int`, represents the 
output of `comparer`.

This would allow me to write some client code that looks something like this:

````cs
var list = new GenericList<int>();
// ...
list.BubbleSort((a, b) => {
    if (a < b) return -1;
    if (a > b) return 1;
    return 0;
});
// ...
````

This is nice and all, but my list isn't written in C#. How can I get this
same type of structure in Java?

### Java Comparer Parameter

Java has a class, [`Function`][function], that's very similar to C#'s `Func`! There's just
one problem: Java's `Function` only supports two type parameters, one for 
input and one for output.

If I tried this in Java, I would get a compilation error:

````java
public class GenericList<T> {
    public void bubbleSort (Function<T, T, int> comparer) {
        // Wrong number of type arguments: 3; required: 2
    }
}
````

After some Googling and poking around StackOverflow, I finally found the
[`BiFunction`][bifunction] class, which does exactly what I need it to do! `BiFunction`
will take exactly three parameter types: The first two being the function
parameters, and the last being the output type of the function.

````java
public class GenericList<T> {
    public void bubbleSort (BiFunction<T, T, int> comparer) {
        // ... Some sorting code ...
    }
}
````

Now with this function signature, any client code now only needs to supply a 
comparer to sort their generic list!

````java
// Sort Integer using the default comparer
GenericList<Integer> list = new GenericList<Integer>();
list.bubbleSort(Integer::compare);
````

````java
// Sort WereWolves by the size of their teeth
GenericList<WereWolf> list = new GenericList<WereWolf>();
list.bubbleSort((wolf1, wolf2) -> {
    if (wolf1.teeth.size < wolf2.teeth.size) return -1;
    if (wolf1.teeth.size > wolf2.teeth.size) return 1;
    return 0;
});
````


[bifunction]: https://docs.oracle.com/javase/8/docs/api/java/util/function/BiFunction.html
[function]: https://docs.oracle.com/javase/8/docs/api/java/util/function/Function.html