---
layout: post
title:  "Form 2 Parameters in ClojureScript"
date:   2022-01-31 7:52:05 -0400
categories: clojure clojurescript apprenticeship
---

Today I was working on a bug that was caused by my component not
utilizing the most recent rendering of a resource. After messing
around a bit with the component's parameters, I realized 
the problem and how it needed to be fixed.

Let's say I have a `person` map with the following structure:

````clojure
{:kind           :person
 :name           "Ronald McDonald"
 :age            42
 :favorite-color "red"}
````

Now let's say I have this component that changes the name of a person:

````clojure
(defn name-input [person]
  (let [person-atom (reagent/atom person)]
    (fn []
      [:form
       [:fieldset
        [:input
         {:type "text"
          :on-change (partial change-name person-atom)
          :value     (:name @person-atom)}]]])))
````

As this component currently exists, any time the textbox is changed, some 
sort of update will occur. So if the text is changed to `Ronald Reagen`, 
then `person-atom` will be updated with the name, Ronald Reagen, and 
further updates can be sent over to a server for further processing.

Now what if somewhere else in the code, the age was changed to `35`? How does
that affect our current data? Well, our `name-input` was initialized with
the entire `person`, in the `person-atom`. So this will not change and will
retain `42` as the age. 

Our data is inconsistent–This is not good! In one part of the code, 
the age is 35 but in another part it's still 42! What can we do to solve this?
Notice how in our component, we're returning a function. 

This function can take parameters, which will be identical to the parameters
passed into the `name-input` function. Each time the component is re-rendered,
the latest `person` will be passed into that function.

So instead of creating a `person-atom`, let's create an atom for the `name`
and pass both the `person` _and_ the `name` in to the `:on-change` function.

````clojure
(defn name-input [person]
  (let [name (reagent/atom (:name person))]
    (fn [person]
      [:form
       [:fieldset
        [:input
         {:type "text"
          :on-change (partial change-name person name)
          :value     @name}]]])))
````

And that's it! Now any time our component is re-rendered, our `:on-change`
function will have a `person` that is consistent with the rest of the application.
