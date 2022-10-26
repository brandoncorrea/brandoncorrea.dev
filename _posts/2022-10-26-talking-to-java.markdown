---
layout: post
title:  "Talking to Java"
date:   2022-10-26 7:52:05 -0400
categories: clojure
---

## Java Interop

Suppose there's a Java class or function that I'd like to use.

````java
JsonObject myObj = new JsonObject()
JsonObject withParams = new JsonObject("{\"some\": \"json\"}")
````

In Clojure, you can instantiate objects like this.

````clojure
(let [my-obj      (JsonObject.)
      with-params (JsonObject. "{\"some\": \"json\"}")])
````

In Java, you'd invoke methods like this.

````java
JsonObject myObj = new JsonObject()
myObj.put("number", 1).put("text", "abc")
````

In Clojure, this would look something like this

````clojure
(let [my-obj (JsonObject.)]
  (.put (.put my-obj "number" 1) "text" "abc"))
````

This can end up creating long lines of code, so the threading operator helps
us take advantage of vertical space.

````clojure
(let [my-obj (JsonObject.)]
  (-> my-obj
      (.put "number" 1)
      (.put "text" "abc")))
````

If this is all we're doing to the object, then we don't really even need the `let` form.

````clojure
(-> (JsonObject.)
    (.put "number" 1)
    (.put "text" "abc"))
````

You can also call static methods in a Java class from Clojure.
What you would normally write in Java...

````java
JsonObject.mapFrom(...)
````

...would look like this in Clojure.

````clojure
(JsonObject/mapFrom ...)
````
