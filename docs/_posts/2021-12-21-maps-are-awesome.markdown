---
layout: post
title:  "Maps are Awesome"
date:   2021-12-21 7:52:05 -0400
categories: clojure java apprenticeship
---

There are some types that will work fine with Java's interop, and others 
that just won't work so well. For example, I can pass a string down to 
a Java function just fine, but if I wanted to pass an Object through,
I'd have to instantiate it first with `MyObject.`.

As it also turns out, maps can be passed down to Java code without any
type casting, assuming the types used in the map are also native to Java.

So let's suppose I had this Java function that I wanted to use in my Clojure app:

````java
public static String findByPhoneNumber(String phoneNumber, Map<String, String> phoneBook) {
    return phoneBook.get(phoneNumber);
}
````

Rather than worrying about type casting my Map, I can just pass it right along
and Java will accept it as a `Map`.

````clojure
(def phone-book
  {"5550123" "John"
   "5550124" "Bill"
   "5557218" "Richard"})

=> (JavaClass/findByPhoneNumber "5550124" phone-book)
"Bill"
````

Now what if you had Person objects as your values, or some other concrete type?

````java
public static String findByPhoneNumber(String phoneNumber, Map<String, Person> phoneBook) {
    return phoneBook.get(phoneNumber);
}
````

Well, Clojure really doesn't care unless Java is accessing and using that
type directly. The concrete value will just be cast to an Object type,
and that Object will be returned rather than trying to force whatever value
is there to be a Person object.

````clojure
(def phone-book
  {:add #(+ 10 %)
   [0 0] :center
   "5550123" "John"
   "5550124" "Bill"
   "5557218" "Richard"})

=> (JavaClass/findByPhoneNumber "5557218" phone-book)
"Richard"
````

Of course, everything has their limits...
````clojure
=> (JavaClass/findByPhoneNumber :add phone-book)
clojure.lang.Keyword cannot be cast to java.lang.String
=> (JavaClass/findByPhoneNumber [0 0] phone-book)
clojure.lang.PersistentVector cannot be cast to java.lang.String
````

But if you make your parameters a little more generic, then you have a lot
more flexibility with what you can do!

````java
public static String findByPhoneNumber(Object phoneNumber, Map<String, Person> phoneBook) {
    return phoneBook.get(phoneNumber);
}
````

````clojure
=> ((JavaClass/findByPhoneNumber :add phone-book) 25)
35
=> (JavaClass/findByPhoneNumber [0 0] phone-book)
:center
````
