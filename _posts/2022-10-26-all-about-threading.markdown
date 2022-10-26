---
layout: post
title:  "All About Threading"
date:   2022-10-26 7:52:05 -0400
categories: clojure
---

Thread operators are intended to help with readability. While they can sometimes
wind up as (visually) larger blocks of code, these larger blocks are usually
easier to follow.

### Thread-First `->`

Passes the result of each form as the first parameter to the next form.

This can make reading forms easier. Rather than reading from the innermost form
outwards, you can read everything from left-to-right. This also helps group
parameters with their respective functions.

````clojure
(defn find-user 
  "Finds a user by id"
  [user-id]
  ;...
  )

(def user-id 1000)

(defn first-name-1 
  "Which parameters go to what function?!"
  [user-id]
  (first (split (:name (find-user user-id)) #" ")))

(defn first-name-2
  "Now I can visualize the flow of information."
  [user-id]
  (-> user-id find-user :name (split #" ") first))
````

### Thread-Last `->>`

Passes the result of each form as the last parameter to the next form.

This is most useful when working with functions that operate on collections
(`map`, `filter`, `reduce`, etc.), but will work in any case when you need your parameters to come last.

````clojure
(defn sum-of-natural-numbers-1 
  "It's almost as if I'm reading this from right-to-left..."    
  [numbers]
  (reduce + (filter integer? (filter pos? numbers))))

(defn sum-of-natural-numbers-2 
  "Now I can read it top to bottom."
  [numbers]
  (->> numbers
       (filter pos?)
       (filter integer?)
       (reduce +)))
````

### Thread-As `as->`

Assigns a name to the result of each form, which is passed down to the next form.

This is most useful when the parameters appear in different positions for each form
in the thread.

````clojure
(defn average-age-1 
  "This can be hard to follow as is."
  [users]
  (/ (reduce + (map :age users)) (count users)))

(defn average-age-2 
  "The thread-as operator allows you to thread forms like the one above."
  [users]
  (as-> (map :age users) $
        (reduce + $)
        (/ $ (count users))))

(defn average-age-3 
  "You can name your variable whatever you'd like"
  [users]
  (as-> (map :age users) i-am-just-another-variable
        (reduce + i-am-just-another-variable)
        (/ i-am-just-another-variable (count users))))
````


### Thread-Conditionally – `cond->` and `cond->>`

Looks like `cond`, but works as a thread. Parameters for `cond->` and `cond->>`
are passed in the same way as `->` and `->>`, respectively.

This is one way you could conditionally perform an operation.

````clojure
(defn assign-permission [user permission] 
  (update user :permissions conj permission))

(defn maybe-assign-read 
  "If the user can read, let them read."
  [user]
  (if (allow-read? user)
    (assign-permission user :read)
    user))

(defn maybe-assign-write
  "If the user can write, let them write."
  [user]
  (if (allow-write? user)
    (assign-permission user :write)
    user))

(defn maybe-assign-delete
  "If the user can delete, let them delete."
  [user]
  (if (allow-delete? user)
    (assign-permission user :delete)
    user))

(defn assign-permissions-1 
  "Assign a user some permissions...maybe"
  [user]
  (-> user maybe-assign-read maybe-assign-write maybe-assign-delete))
````

However, `cond->` eliminates the need for those extra "conditional" functions.

````clojure
(defn assign-permission [user permission]
  (update user :permissions conj permission))

(defn assign-permissions-2
  "Assign a user some permissions...maybe"
  [user]
  (cond-> user
    (allow-read? user) (assign-permission :read)
    (allow-write? user) (assign-permission :write)
    (allow-delete? user) (assign-permission :delete)))
````

### Thread-Some – `some->` and `some->>`

Works exactly the same as `->` and `->>`, except these will stop execution
upon encountering a `nil` value.

This is most useful when you want to perform some operation without
having to constantly check for nils.

For example, mathematical operations don't like `nil`.

````clojure
=> (+ nil 1)
Execution error (NullPointerException) at user/eval2068 (REPL:1).
null
````

But by using `some->`, we can at least avoid exceptions being thrown.

````clojure
=> (some-> nil (+ 1))
nil
````

This is also quite useful when you want to conditionally perform some operation.

````clojure
(defn award-winner-1 [competitors]
  (when-let [winner (first users)]
    (award-gold-medal winner)))

(defn award-winner-2 [competitors]
  (some-> first award-gold-medal))
````

`some->` and `some->>` will eliminate the need for `when-let` forms like the one above,
minimizing the amount of code you need to write.
