---
layout: post
title:  "Setting Up Quil"
date:   2021-12-02 7:52:05 -0400
categories: clojure apprenticeship
---

Quil is a UI library for Clojure that I've recently started working with.
I'll go over some basic setup steps and areas where I got stuck...

### ClassPath Exception (or something)!

I'll save you the trouble of reading through the rest of the post and 
get this out of the way first. I spent hours trying to resolve some 
strange exceptions I was getting when trying to run any Quil project. 
This had to do with the JVM version I had running with lein.

Quil (and some other libraries) seem to work best under Java 1.8. So we'll
need to update some things to use that version.

If you open your repl, you should see a line like this print out:
- `OpenJDK 64-Bit Server VM 17.0.1+0`

You may see a different version, but it should have something like
"Server VM" in it. This is the Java version that is running in your repl
or app. Even though your `java -version` may show one thing, this may
be using something completely different.

If you follow the steps in this [StackOverflow][stackoverflow-jvm] question, 
the issue should resolve and allow you to run Quil. Otherwise you can continue 
below to see the steps I followed on MacOS.

#### Setting Java to 1.8

In the Terminal:
- ``export JAVA_HOME=`/usr/libexec/java_home -v 1.8` ``
  - Be sure to include backticks `` ` `` and not single-quotes `'`
- `export JAVA_HOME=$(/usr/libexec/java_home -v 1.8)`
- `nano ~/.zshrc`
  - Write the following to the file:
  - `export JAVA_HOME=$(/usr/libexec/java_home -v 1.8.0)`
  - Save the file and exit: `CTRL+X Y`
- `source ~/.zshrc`

After performing those steps, these commands should show Java version 1.8
- `echo $JAVA_HOME`
- `java -version`

Your repl should now also reflect version 1.8. If Quil wasn't working before because
of a JVM issue, it _should_ start working now.

### Running Quil

You may have already noticed, but if you executed `lein run` right after creating a
`lein new quil my-project`, you probably saw something like this print out to the console:
`No :main namespace specified in project.clj.`

This is because the default project scaffolding wants you to evaluate the core.clj 
file instead. You can do this a couple different ways:
1. Navigate to core.clj in your editor > Right Click > Click `Run project-name.core`
2. Run Terminal:
   - `lein repl`
   - `(require 'my-project.core)`

### Setting up `lein run`

I personally like being able to just type `lein run` in the command prompt. 
So I'll explain a quick way to do that.

If you open `project.clj`, set `:main` to `my-project.core` (whatever you originally named
your project).

````clojure
(defproject my-project
  :main my-project.core
  ; ...
  )
````

Next you'll want to open that `core.clj` file and add your main function. You should
already see a `defsketch` item if you're using the scaffold project. In your newly created 
`-main` function, just place the name of that sketch in the function body.

````clojure
(q/defsketch my-project
  ; ...
  )

(defn -main [] my-project)
````

Doing this should allow you to `lein run` the project, just as if you evaluated the file–and
it's as easy as that! Now go check out the functions available for Quil and start experimenting 
with Quil's awesome features!

[quil]: http://quil.info
[stackoverflow-jvm]: https://stackoverflow.com/questions/21964709/how-to-set-or-change-the-default-java-jdk-version-on-macos