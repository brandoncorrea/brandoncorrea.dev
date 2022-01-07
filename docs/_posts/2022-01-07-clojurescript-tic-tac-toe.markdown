---
layout: post
title:  "ClojureScript Tic Tac Toe!"
date:   2022-01-07 7:52:05 -0400
categories: clojure apprenticeship
---

I just finished making my first [ClojureScript][clojurescript] project by 
creating a new UI for Tic Tac Toe! Here I used [Reagent][reagent], which is 
a sort of bridge between React and Clojure.

Of course, being new to ClojureScript, I ended up running into a few problems.
I had found several ways of compiling ClojureScript to JavaScript, but I 
couldn't find any way to run tests while also using Reagent code–none of my
Google search solutions seemed to work.

Luckily, a coworker of mine has this awesome 
[ClojureScript sample project][cljs-sample-project] that stubs out all the 
main components needed to get ClojureScript tests up and running! 
Unfortunately though, I still had some trouble adding this to my existing 
Tic Tac Toe project. There were certain libraries that other interfaces depended
on that ClojureScript just did not like.

This was a good opportunity for me to learn how to publish a public library.
I ended up pulling out my core game logic into a new [ttt-core][ttt-core] 
project and publishing it to [Clojars][clojars]. After that, I was able to replace the 
original project's game logic with that library as well as import it into 
a fresh ClojureScript project!

Finally, I was ready to start writing ClojureScript. The terminal prompt 
was blinking and the test output was red... a wonderful sight indeed.

<div id="app"></div>
<script src="/assets/js/tic-tac-toe.js" type="text/javascript"></script>

[reagent]: https://github.com/reagent-project/reagent
[cljs-sample-project]: https://github.com/mdwhatcott/cljs-sample-project
[ttt-core]: https://github.com/brandoncorrea/ttt-core
[clojars]: https://clojars.org
[clojurescript]: https://clojurescript.org