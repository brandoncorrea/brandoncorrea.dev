---
layout: post
title:  "Decomposing Markup Code"
date:   2022-02-10 7:52:05 -0400
categories: apprenticeship
---

Today I was shown a different way of composing Hiccup code. As developers our first 
thought is to take apart and abstract code for reusability and quick updates, but should
we be doing this with Hiccup, or really any other type of markup/rendering language?

Here's some Hiccup content with some common content pulled out into a function 
for repeat use.

````clojure
(defn learn-about [kind]
  #_do-some-learning)

(defn objectively-good-food [kind name description]
  [:div.content
   [:h2 name]
   [:h4 {:on-click #(learn-about kind)} "Learn more"]
   [:p description]])

(defn homepage []
  [:div#content.container
   [:div.another-container
    [:h1 "Food"]
    [:p "Foods that are objectively delicious"] 
    [objectively-good-food :coffee "Coffee" 
     "Coffee is one of the greatest things to be brewed by man"]
    [objectively-good-food :chocolate "Chocolate" 
     "Chocolate anything is delicious–even chocolate steak"]
    [objectively-good-food :espresso "Espresso" 
     "Coffee may be good, but it gets better under pressure!"]]])
````

With my programmer mind, I'm even tempted to do some more refactoring, because this 
_could_ be broken down even more.

````clojure
(defn learn-about [kind]
      #_do-some-learning)

(def best-foods 
  [{:kind        :coffee
    :name        "Coffee"
    :description "Coffee is one of the greatest things to be brewed by man"}
   {:kind        :chocolate
    :name        "Chocolate"
    :description "Chocolate anything is delicious–even chocolate steak"}
   {:kind        :espresso
    :name        "Espresso"
    :description "Coffee may be good, but it gets better under pressure!"}])

(defn objectively-good-food [{:keys [kind name description]}]
  [:div.content
   [:h2 name]
   [:h4 {:on-click #(learn-about kind)} "Learn more"]
   [:p description]])

(defn homepage []
  [:div#content.container
   [:div.another-container
    [:h1 "Food"]
    [:p "Foods that are objectively delicious"]
    (for [food best-foods]
      [objectively-good-food food])]])
````

There. Perfectly composable. Okay but now I need to add a special class to some elements
in coffee, because the client ***really*** likes coffee. Well, there are some tricks we 
could do to force the machine to do our will, but that can be error-prone and a pain to 
implement. Now we have tests around whether this function renders a special 
class–a cosmetic issue that we need not trouble ourselves with.

Also, try to follow this code. Do you know (roughly) what the page will look like 
at first glance? Do you know where to add new food content? Markup languages are 
typically pretty easy to read and follow, and I believe that was the intent. 
So let's see what this would look like if it was all put back together.

````clojure
(defn learn-about [kind]
  #_do-some-learning)

(defn homepage []
  [:div#content.container
   [:div.another-container
    [:h1 "Food"]
    [:p "Foods that are objectively delicious"]
    [:div.content
     [:h2 "Coffee"]
     [:h4 {:on-click #(learn-about :coffee)} "Learn more"]
     [:p "Coffee is one of the greatest things to be brewed by man"]]
    [:div.content
     [:h2 "Chocolate"]
     [:h4 {:on-click #(learn-about :chocolate)} "Learn more"]
     [:p "Chocolate anything is delicious–even chocolate steak"]]
    [:div.content
     [:h2 "Espresso"]
     [:h4 {:on-click #(learn-about :espresso)} "Learn more"]
     [:p "Coffee may be good, but it gets better under pressure!"]]]])
````

Not bad–it's all Hiccup! There's really only one functional piece to this: `learn-about`.
Now we can add whatever crazy styles the client wants to whatever elements we want. We
can change the structure of the view without affecting the logic behind how it is rendered.
We can **visualize** how the page will look before it's even rendered!