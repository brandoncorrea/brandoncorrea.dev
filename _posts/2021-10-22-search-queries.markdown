---
layout: post
title:  "Search Queries"
date:   2021-10-22 7:52:05 -0400
categories: java apprenticeship
---

Today I started implementing Java's [List][list-interface] interface using a
Linked List. I've been able to get through a lot of the basic functions without
too many issues, but I started to notice some code duplication that differed
in very minor ways.

### Sad Looping

Below is the general structure these functions operated, where first represents
the first node in the linked list.

````java
// Do something for each element
for (LinkedNode node = first; node != null; node = node.next) {
    // Do something ...
}
````

````java
// Check the list for a matching predicate
LinkedNode node = first;
while (node != null; node = node.next) {
    if (/* Check for Something */)
        return node;
    node = node.next;
}
// Do more stuff ...
````

This wouldn't be that big of an issue for me if it were only in a couple 
functions, but just about every other function I had needed some sort of loop 
like these.

This was adding several lines to each function, included iteration logic, null 
checking, and tons of curly braces - all making it difficult to see what the 
function is actually doing.

### Happy Looping

Coming from C#, there was a [LINQ library][linq-library] I used in just about 
every project. This provided search and iteration methods for just about 
every collection type, which is exactly the kind of thing I would need here.

LINQ methods typically accept some function or predicate (usually Lambda) to 
use when iterating over a collection. This was easy enough to do in Java,
especially with the [Function][function-interface] and 
[Consumer][consumer-interface] interfaces it provides!

Here are the final implementations of my pseudo "linq" functions.

````java
private LinkedNode<TValue> find(Function<LinkedNode<TValue>, Boolean> predicate) {
    for (LinkedNode<TValue> node = first; node != null; node = node.next)
        if (predicate.apply(node))
            return node;
    return null;
}

private void forEach(Consumer<LinkedNode<TValue>> action) {
    for (LinkedNode<TValue> node = first; node != null; node = node.next)
        action.accept(node);
}
````

This allows me to break out all that looping logic from the majority of the 
functions, making them much easier to read and understand. In fact, `find` 
reduced a couple of the List functions to just a single line!

Here are a couple examples where I was able to use these functions.

````java
private LinkedNode<TValue> findNodeWithValue(Object value) {
    return find(node -> valuesEqual(node.value, value));
}

public Object[] toArray() {
    Object[] list = new Object[size];
    AtomicInteger index = new AtomicInteger(0);
    forEach(node -> list[index.getAndIncrement()] = node.value);
    return list;
}
````

As we can see in `toArray`, there is one downfall to using Lambda expressions
here. For some reason, Java doesn't seem to like it when we use local value 
type variables in Lambda expressions. If I want to modify a variable locally, 
I would need to give it a reference type instead, 
like [AtomicInteger][atomic-integer].

[list-interface]: https://docs.oracle.com/javase/8/docs/api/java/util/List.html
[linq-library]: https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/linq/
[function-interface]: https://docs.oracle.com/javase/8/docs/api/java/util/function/Function.html
[consumer-interface]: https://docs.oracle.com/javase/8/docs/api/java/util/function/Consumer.html
[atomic-integer]: https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/atomic/AtomicInteger.html
