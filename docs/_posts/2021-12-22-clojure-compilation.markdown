---
layout: post
title:  "Clojure Compilation"
date:   2021-12-22 7:52:05 -0400
categories: clojure, apprenticeship
---

I've heard that Clojure can be compiled into a JAR file, but never actually
tried it before. So can we do this?

First, we need a project to compile. We'll go with the classic "Hello, World!"
app.

````
> lein new hello-world
> cd hello-world
````

Now, if your Clojure code doesn't have a `-main` method and is just a library,
then you can just run `lein uberjar`, and you should see some output like this:

````
> lein uberjar
Created /Users/Projects/hello-world/target/hello-world-0.1.0-SNAPSHOT.jar
Created /Users/Projects/hello-world/target/hello-world-0.1.0-SNAPSHOT-standalone.jar
````

Once you have that, you can use the JAR wherever you need it!

### Compiling with -main

If you have a `-main` method somewhere, then there are a couple extra steps 
that need to be taken. First, you'll need to add `(:gen-class)` to the top
of your main Clojure file:

````clojure
(ns hello-world.core
  (:gen-class))

(defn -main []
  (println "Hello, World!"))
````

Without this `(:gen-class)` tag, uberjar won't recognize your `-main` 
function and your project won't compile properly.

Next, you'll need to update your project.clj with a couple configurations:
`:main` and `:aot`, both with the namespace that your `-main` methods exist.

````clojure
(defproject 
  ; ...
  :main hello-world.core
  :aot [hello-world.core])
````

`:main` tells uberjar what namespace to start the project in. The project will
still compile without this setting, but instead of your application you'll
get a nice little REPL prompt.

`:aot` isn't required at this time; your JAR will still run without it. 
However, if you exclude it then you'll get a lengthy warning message 
like this:

````
> lein uberjar
Warning: specified :main without including it in :aot. 
Implicit AOT of :main will be removed in Leiningen 3.0.0. 
If you only need AOT for your uberjar, consider adding :aot :all into your
:uberjar profile instead.
````

Once you add those settings to project.clj, you're ready to compile and run!

````
> lein uberjar
Compiling hello-world.core
Created /Users/Projects/hello-world/target/hello-world-0.1.0-SNAPSHOT.jar
Created /Users/Projects/hello-world/target/hello-world-0.1.0-SNAPSHOT-standalone.jar
> java -jar /Users/Projects/hello-world/target/hello-world-0.1.0-SNAPSHOT-standalone.jar
Hello, World!
````

Again, you'll want to run your application from the "standalone" jar.
Otherwise you'll get an error like this:

````
> java -jar /Users/Projects/hello-world/target/hello-world-0.1.0-SNAPSHOT.jar
Exception in thread "main" java.lang.NoClassDefFoundError: clojure/lang/Var
        at hello_world.core.<clinit>(Unknown Source)
Caused by: java.lang.ClassNotFoundException: clojure.lang.Var
        at java.net.URLClassLoader.findClass(URLClassLoader.java:387)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:418)
        at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:355)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:351)
        ... 1 more
````
