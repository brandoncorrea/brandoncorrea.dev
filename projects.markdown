---
layout: page
title: Projects
permalink: /projects/
---

## [semantic-ui-clj](https://github.com/brandoncorrea/semantic-ui-clj)

A Clojure interface for [Semantic UI](https://semantic-ui.com/).
Written in Clojure-Common (cljc) for use in both web and
server-size development.

Something like this...

````clojure
[container {:fluid true} 
 [header {:as :h1 :text-align :center} "Welcome"] 
 [button {:size :massive} "Click Me!"]]
````

... Should translate to this ...

````html
<div class="ui fluid container">
 <h1 class="ui center aligned header">Welcome</h1>
 <button class="ui massive button">Click Me!</button>
</div>
````

## [Discord Noob Bot](https://github.com/brandoncorrea/Noob)

I've recently started creating a Discord Bot themed after 
glorifying the "Noob" over the "Pro". You can join this 
[Discord Channel](https://discord.gg/9bWWgezdjy) if you'd like to toy around with it!

## [Performance Scripts](https://github.com/brandoncorrea/performance)

Just some scripts for performance and load testing...
