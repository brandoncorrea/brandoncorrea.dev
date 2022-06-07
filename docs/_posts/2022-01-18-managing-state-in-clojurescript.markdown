---
layout: post
title:  "Managing State in ClojureScript"
date:   2022-01-18 7:52:05 -0400
categories: clojure, apprenticeship
---

I've noticed a couple prominent forms of data management in ClojureScript
projects. First is the "Form 2" structure, where you manage state within 
a Reagent atom and return a function. Second is managing state in the 
namespace.

### Form 2 State Management

Let's say we have a counter element that increments a counter when clicked. 
Using the Form 2 structure, we can implement this like so:

````clojure
(defn counter-button []
  (let [counter (reagent/atom 0)]
    (fn [_]
      [:p {:on-click #(swap! counter inc)} @counter])))
````

This function creates a Reagent atom starting at 0, and every time the `p`
element is clicked, the count increments and the DOM is updated with the 
new count.

This state management structure comes with some pros and cons.

Pros:
- Data is only retained as long as it is needed (no hanging on to unused data in memory)
- Nothing else knows about `counter`–we can create 100 different counters 
displaying 100 different values!

Cons:
- Data is lost after the component is removed from the DOM.
- Nothing else knows about `counter`. If another part of the code needs to
know the current value of `counter`, this structure would make that task difficult.

One way we could address some of these issues is to retain the value of
`counter` in a parent element.

````clojure
(defn counter-button
  ([] [counter-button (reagent/atom 0)])
  ([counter]
    (let [_ counter]
      (fn [_]
        [:p {:on-click #(swap! counter inc)} @counter]))))

(defn parent []
  (let [counter (reagent/atom 0)]
    (fn []
      [:div#container
       [counter-button counter]
       [:h1 (str "The count is " @counter)]])))
````

While this may work fine over two components, continuing in this structure
in more complex applications can end up in a large chain of data being passed
down to lower-level components.

### Namespace Management

If you don't like the idea of passing data down a large chain of functions,
you may elect to manage your state in atoms owned by the namespace.

````clojure
(def counter (reagent/atom 0))
(defn counter-button []
  [:h1 {:on-click #(swap! counter inc)} @counter])
````

Using this structure, you just create an atom somewhere within your namespace
and use it in your components. No need for any `let` `fn` structures.

As with Form 2 components, this also have some pros and cons.

Pros:
- Data persists throughout the lifetime of the application 
(basically until the page is refreshed)
- The scope of `counter` is not limited to the component–just about anything
can reference its value.

Cons:
- Data persists throughout the lifetime of the application
  - This _can_ be bad if your atoms are very resource-heavy and
  end up causing performance issues.
- This can hinder the component's reusability. Since the component references 
a named atom, every counter will render the same value.
  - This may be a desirable trait if you are looking for consistency across 
  specific components.

I feel somewhat partial to the "Form 2" style given the reusability of 
components as well as the functional feel that they have. Although, there 
also seems to be quite a bit of power in managing state under namespaces.
You really just gotta take a quick look at the problem and pick the best 
tool for the job.
