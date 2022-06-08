---
layout: post
title:  "Iterators"
date:   2021-10-26 7:52:05 -0400
categories: java, apprenticeship
---

Today I was working on implementing the [List][list-interface] interface in 
Java and encountered a method I was unfamiliar with, iterator. The iterator
function is actually declared in an interface farther down the chain, 
[Iterable][iterable-interface].

The iterator method just returns an [Iterator][iterator-interface] instance 
for the iterable type. But what does is that iterator actually used for, and
why would we use this?

### What is an Iterator?

As it turns out, the Iterator is just used for reading over a collection 
and returning one value at a time. For some reason it took me a while to 
understand this.

While the iterator contains just a handful of seemingly simple methods, they 
are incredibly useful for the majority of methods needed for collection types; 
most can be implemented using just Iterator.

For example, a collection could implement this `contains` method using an
iterator. Since this doesn't tell us anything about how the list is 
implemented, this will work for any Iterable class.

````java
public boolean contains(Object value) {
    Iterator<T> iterator = iterator();
    while (iterator.hasNext())
        if (Objects.equals(value, iterator.next()))
            return true;
    return false;
}
````

As we can see here, we are returning a value before we finish reading the 
collection. Since `next()` only "realizes" elements when it's called, this 
gives the programmer some control and efficiency on how the collection is read.

### For-Each Looping

One big perk to the Iterable interface is that classes which implement it
can take advantage of for-each looping. The for-each `for` structure allows
you to hide the "Iterator" part, making the code more understandable and 
manageable.

````java
Iterable<T> collection;

// Using the iterator explicitly
Iterator<T> iterator = collection.iterator();
while (iterator.hasNext()) {
    T element = iterator.next();
    /* ... Loop Body... */
}

// Using the for-each method
for (T element : collection) {
    /* ... Loop Body ... */
}
````

Again, this also hides our collection implementation, allowing for a more 
generic usage. Using this for each method, we can avoid off-by-one errors and
any other implementation issues that could occur with manually traversing our
list.

[list-interface]: https://docs.oracle.com/javase/8/docs/api/java/util/List.html
[iterator-interface]: https://docs.oracle.com/javase/7/docs/api/java/util/Iterator.html
[iterable-interface]: https://docs.oracle.com/javase/8/docs/api/java/lang/Iterable.html