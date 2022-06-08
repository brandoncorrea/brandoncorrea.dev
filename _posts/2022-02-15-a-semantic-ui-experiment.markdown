---
layout: post
title:  "A Semantic UI Experiment"
date:   2022-02-15 7:52:05 -0400
categories: clojure apprenticeship
---

UI and design has always been one difficult for me, and when working in
React, [Semantic UI React][semantic-ui-react] was always a nice go-to 
for building responsive user interfaces. However, Semantic UI
doesn't have a Clojure/Hiccup version in [GitHub][semantic-ui-github].

Although the CSS and JS files can always be imported into a project and 
used in the Hiccup options...

````clojure
(defn home []
  [:div {:class "ui center aligned container"}
   [:h1 {:class "ui header"} "Tacos"]
   [:div {:class "ui buttons"}
    [:button {:class "ui primary button"} "Submit"]
    [:button {:class "ui secondary button"} "Cancel"]]])
````

...it would be nice to be able to build the UI like this...

````clojure
(defn home []
  [container {:text-align :center}
   [header {:as :h1} "Tacos"]
   [button-group
    [button {:primary true} "Submit"]
    [button {:secondary true} "Cancel"]]])
````

I'm not quite sure if this is really an improvement though–while
this results in writing less code, there are some redundancies around
the `:primary` and `:secondary` flags' `true` values. Maybe there's a 
better way to set these flags in the component.

Something like this _might_ be okay, but it sort of veers away from your
typical Hiccup syntax of `[name options-map & content]`.

````clojure
(defn home []
  [container {:text-align :center}
   [header {:as :h1} "Tacos"]
   [button-group
    [button :primary "Submit"]
    [button :secondary "Cancel"]]])
````

In any case, I've started [experimenting][semantic-ui-clj] with what a
Clojure library for Semantic UI might look like. 

The idea is that there would be a library fully written in cljc and be
usable in both clj and cljs files, as there may be some instances when you 
need to generate HTML on the server. 

This should be independent of any other framework or library like Reagent,
but should also be extendable with these libraries (or any other Clojure code).
Ideally, the core of this project would be dependent solely on clojure.core.

To start out, I'm just going to implement several Semantic UI components 
and test them out in a dummy project to see if it would even be worth 
implementing the rest of the interface. 

[semantic-ui-react]: https://react.semantic-ui.com/
[semantic-ui-github]: https://github.com/Semantic-Org/Semantic-UI
[semantic-ui-clj]: https://github.com/brandoncorrea/semantic-ui-clj
