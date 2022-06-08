---
layout: post
title:  "Clojure-Ruby Interopability"
date:   2021-12-28 7:52:05 -0400
categories: clojure, ruby, apprenticeship
---

In [yesterday's blog post][clojure-compilation], I talked about how we can 
export Clojure functions in the compiled JAR file. Well today I'd like to see
how we can take that Clojure JAR and use it in our Ruby code.

As a refresher, here's the code that was compiled in the last blog post.

````clojure
(ns hello-world.core
  (:gen-class
    :methods [[hello [String] void]
              ^{:static true} [static_hello [String] void]]))

(defn hello [subject]
  (println (str "Hello, " subject "!")))

(defn -hello [_ subject] (hello subject))
(def -static_hello hello)

(defn -main [& args] (hello "World"))
````

### Setup

First thing we'll need to do is install [JRuby][jruby], if it's not 
already installed. This will allow us to run Ruby under the JVM. I'm 
using Homebrew, so I'll just enter `brew install jruby` in my terminal.

Next, we'll need a Ruby file to run our code. This can be a standalone 
rb file or a Ruby project. For simplicity, I'll just use a single file:
`hello_world.rb`.

In our Ruby file, we will need to import `java` as well as our .jar file.
`java` allows us to use Java libraries within our Ruby module, and the 
.jar reference allows us to use modules within the .jar.

````ruby
require 'java'
require '/path/to/my/hello-world.jar'
````

### Class Aliases

At this point, we're ready to start working with hello-world.core!
If you want to reference a class in a module, Ruby provides several 
aliases.

````ruby
puts Java.hello_world.core
puts Java.Hello_world.core
puts Java::hello_world::core
puts Java::Hello_world::core
````

If we run this code, we can see that each alias points to the 
`Java::Hello_world::core` format.

````
> jruby hello_world.rb
Java::Hello_world::core
Java::Hello_world::core
Java::Hello_world::core
Java::Hello_world::core
````

### Class Execution

Once you have the class reference, you can treat the rest of the statement
just as any other Ruby or Java class!

````ruby
require 'java'
require '/path/to/my/hello-world.jar'

# Main
Java::Hello_world::core.main []

# Static Hello
Java::Hello_world::core.static_hello "from Static"

# Object Reference Hello
core = Java::Hello_world::core.new
core.hello "from Object"
````

`main` will always require some String array argument, even if the argument
isn't used. Static methods can be called directly from the class reference 
(`main` is actually a static method); they do not require a `new` object.
Finally, as you might expect, non-static methods will require a `new` object
reference.

````
> jruby hello_world.rb
Hello, World!
Hello, from Static!
Hello, from Object!
````

Keep in mind that this must be run using `jruby`. If you try running this
with regular ol' `ruby`, then you may get some errors like this.

````
`require': cannot load such file -- /path/to/my/hello-world.jar
````

[clojure-compilation]: https://brandoncorrea.dev/clojure/java/apprenticeship/2021/12/27/more-on-clojure-compilation.html
[jruby]: https://github.com/jruby/jruby