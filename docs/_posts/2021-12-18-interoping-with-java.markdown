---
layout: post
title:  "Interoping with Java"
date:   2021-12-16 7:52:05 -0400
categories: clojure apprenticeship
---

I didn't realize how easy it was to pull external JAR files into a 
Clojure application until today. Let's say I have a .jar file named, 
jar.jar, which has some code for Star Wars' Jar Jar. In project.clj, 
all I need to do is add this line:

````clojure
(defproject
  ; ...
  :resource-paths ["/the/sith/lord/jar.jar"]
  #_...)
````

So let's say the package name of this jar.jar file was StarWars,
and had some classes: `JarJar`, `Gungan`, and `BossNass`. Then 
anywhere in our clojure application, we can `:import` this library
and these classes using this structure:

````clojure
(ns app.some-namespace.core
  (:import [StarWars JarJar Gungan BossNass]))
````

So what if I wanted to instantiate these classes and use BossNass's 
`exile` method on JarJar?

````clojure
(defn exile-jarjar []
  (.exile (BossNass.) (JarJar.)))
````

The prefixed `.` syntax allows us to access methods of a class, and the 
trailing `.` allows us to instantiate Java classes in Clojure.

Now we probably don't want our Java class syntax taking over our Clojure
code. This StarWars package would be most useful in its own Clojure 
namespace for a few reasons:
1. We don't need to use `.` syntax throughout our application
2. We don't need to `:import` our JAR everywhere
3. We can reuse the Clojure namespace in other applications

So what would this look like in its own library? Probably something
like this, but you can really build it however you'd like.

````clojure
(ns star-wars.core
    (:import [StarWars JarJar Gungan BossNass]))

(defn create-jar-jar [] (JarJar.))
(defn create-boss-nass [] (BossNass.))
(defn create-gungan [name] (Gungan. name))
(defn exile [exiler exilee] (.exile exiler exilee))
(defn execute-order [executor order-number]
  (.executeOrder executor order-number))
````

Now we can use this interface to our Java code in our Clojure code!

````clojure
(ns jar-jar-adventure.core
    (:require [star-wars.core :as s]))

(defn -main []
  (let [boss-nass (s/create-boss-nass)
        jar-jar (s/create-jar-jar)]
    (s/exile boss-nass jar-jar)
    ; Jar Jar is a little bitter...
    (s/execute-order jar-jar 66)))
````

At this point, the integration is seamless. The core application has
no idea that these are Java classes!
