---
layout: post
title:  "Testing View Code"
date:   2022-03-03 7:52:05 -0400
categories: clojure, apprenticeship
---

When building out a ClojureScript application, there are certain things we
ought to test, and then there are some seemingly grey areas where we're 
not sure whether it should be tested. 

````clojure
(defn welcome []
  [:div.container
   [:h1 "Welcome to my cool website!"]
   [:h4 "Check out our products for XYZ"]])
````

For example, I don't really care to test this component, because it's all
view code! Sure, I *could* test that the header tags display our welcome 
messages properly, but this is a more volatile part of the code. Any time 
somebody goes in here to change some text, they'd have to go and change the
test, which will say the exact same thing!

I would place these messages on the same level of importance as the `:h1` or `:div`
tags.

````clojure
(defn profile [{:keys [name picture bio]}]
  [:div.container
   [:h1 "User Profile"]
   (when picture [:img {:src picture}])
   [:p name]
   [:p bio]])
````

Now let's take a look at another component, which takes a user and displays 
their information. We would want to test the name, bio, and picture because 
this is information that can change based on the user we provide it.

However, we may not want to test the header message, "User Profile". Again, 
this is view code that we don't want to manage in our tests. All we care 
about is how the component changes when we give it different parameters.
