---
layout: post
title:  "Keeping Reagent Forms Separate"
date:   2022-01-11 7:52:05 -0400
categories: clojure apprenticeship
---

Do you ever spend WAY too much time debugging an issue only to find
out that the cause was from a minor error you made somewhere down the line?
Yeah, well that happened today...

I was updating some reagent components and the browser wasn't re-rendering
things properly. Yet, I was using the same structure as another (working)
part of the code, so in theory this should be working the same wat. 
Well, it wasn't.

Here's more or less what the broken code looked like:

````clojure
(defn greeting [state]
  (if (say-hello? state)
    [hello state]
    (let [english? (reagent/atom true)
          state-atom (reagent/atom state)]
      (fn [_]
        (if @english?
          [goodbye state-atom #_change-event #(reset! english? false)]
          [au-revoir state-atom #_change-event #(reset! english? true)])))))
````

Within each component, there are API calls that should to trigger 
changes back on the UI. The issue was that changes were being sent
to the server, but nothing was happening on the browser to reflect 
those changes. 

So of course, my first thought was to investigate all my API calls and 
make sure those looked right – they were fine. So I went back to square one
and started changing small bits and pieces until the issue surfaced, which
brought me to the code above.

The issue had somethign to do with mixing different 
[component forms][reagent-forms] in the same component. I'm not sure what 
exactly was happening under the hood, but moving the Form 2 component into a
new components seemed to fix the issue.

````clojure
(defn farewell [state]
  (let [english? (reagent/atom true)
        state-atom (reagent/atom state)]
    (fn [_]
      (if @english?
        [goodbye state-atom #_change-event #(reset! english? false)]
        [au-revoir state-atom #_change-event #(reset! english? true)]))))

(defn greeting [state]
  (if (say-hello? state)
    [hello state]
    [farewell state]))
````

Coincidentally, this actually results in more organized code. The `greeting`
doesn't really care what happens after the `let` `fn`–before it was just 
polluted with lower-level details. It also puts our "goodbye" components
in one place, rather than having it mixed in with a hello component.

[reagent-forms]: https://github.com/reagent-project/reagent/blob/master/doc/CreatingReagentComponents.md