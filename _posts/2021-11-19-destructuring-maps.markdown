---
layout: post
title:  "Destructuring Maps"
date:   2021-11-19 7:52:05 -0400
categories: clojure apprenticeship
---

Yesterday I wrote about how 
[Destructuring Collections][destructuring-collections] works in Clojure.
Today I'll talk about how you can destructure maps!

Early on in my Clojure journey, maps seemed a bit foreign to me.
After using them more and more, they're starting to make a lot more 
sense, and now I'm finally starting to understand how to destructure maps!

### Selecting Keys

When destructuring collections, we get values in the order they appear in 
the collection. You could say that the index of the value is the key. 

Destructuring maps is similar in the sense that you select values using keys.
So when we destructure maps in a function's parameter list, we would just
specify the keys that we want to select. 


````clojure
(defn introduce-pet [{name :name age :age kind :kind}]
  (str "My " kind ", " name ", is " age " years old!"))
````

Now we can pass any map that contains the keys `:name`, `:age`, and `:kind`
to introduce a pet!

````clojure
user=> (introduce-pet {:name "Wolfy" :age 3 :kind "dog"})
"My dog, Wolfy, is 3 years old!"
````

Something's bothering me here... I'm basically writing everything twice,
`name :name`, `age :age`, and `kind :kind`. To fix this, we can just update
our parameter list with `:keys` to skip all that extra naming!

````clojure
(defn pet-greeting [{:keys [name age kind]}]
  (str "My " kind ", " name ", is " age " years old!"))
````

This small change leaves our function body and interface unaffected, 
while also cleaning up our declaration!

### Destructuring `:as`

As I mentioned in the destructuring collections post, you can store the 
parameter as a named value, while also destructuring its parts using the
`:as` keyword! Here's how you would do that with maps:

````clojure
(defn describe-puppy [{:keys [age] :as dog}]
  (assoc dog :age-description
    (cond (< age 2) "puppy"
          (< age 7) "older puppy"
          :else "old puppy")))
````

While we're only selecting `age` in our map, the `:as` keyword will still
pull back and store the entire map. So when we call this function,
what we get back is the original map with a new key-value pair!

````clojure
user=> (describe-puppy {:name "Wolfy" :age 3 :kind "dog"})
{:name "Wolfy", :age 3, :kind "dog", :age-description "older puppy"}
````

Or if we wanted to determine which puppy has more tenure:

````clojure
(defn oldest [{age-a :age :as a} {age-b :age :as b}]
  (if (> age-a age-b) a b))
user=> (oldest {:name "Lucky" :age 11} {:name "Max" :age 5})
{:name "Lucky", :age 11}
````

### Destructured Bonding

Now to put all our newfound destructuring knowledge together, 
let's make a function to create a customer description string!

I have a customer named Philip that I want to describe, but only 
if he is a valid customer. Let's just ignore the security holes in 
this data structure for now...

What follows is a declaration for philip and a function that destructures 
everything we need from our customers: `name` and `card & rest-cards`, 
their `:address` parts, and also stores the parameter `:as customer`.

````clojure
(def philip
  {:name "Philip Sherman"
   :card-numbers [4111111111111111
                  4111111111111112
                  4111111111111113]
   :address {:street "42 Wallaby Way"
             :city "Sydney"
             :state "New South Wales"}
   :favorite-color :red})

(defn customer-description
  [{name :name
    [card & rest-cards] :card-numbers
    {:keys [street city state]} :address
    :as customer}]
  (if (valid-customer? customer)
    (str name " lives in " street ", " city ", " state ". "
         name "'s default card number is " card
         ", and has " (count rest-cards) " more cards available.")
    "Invalid Customer!"))

user=> (customer-description philip)
"Philip Sherman lives in 42 Wallaby Way, Sydney, New South Wales. Philip Sherman's default card number is 4111111111111111, and has 2 more cards available."
````

In the example above, not only are we able to use our map destructuring 
skills, but we can also destructure other maps and collections contained
in our map!

Now while we _can_ put all of our destructuring logic into a single function,
it's often better to break things out into smaller functions. As you can 
probably see, our parameter list is a bit clunky and hard to understand 
at first.

But hopefully this demonstrated the power of destructuring parameters,
and you're able to take something away from this for the betterment of 
your code!

[destructuring-collections]: https://brandoncorrea.dev/clojure/apprenticeship/2021/11/18/destructuring-collections.html
