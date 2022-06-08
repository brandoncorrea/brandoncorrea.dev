---
layout: post
title:  "Serving up Hiccups!"
date:   2021-12-10 7:52:05 -0400
categories: clojure apprenticeship
---

I've heard of Hiccup and seen some examples here and there, but never
actually used it. So what on earth IS it, and how do I build a website 
with it?

### What is Hiccup?

My first impression of Hiccup was that it was a full-fledged web server
technology that could be used to spin up a server and start serving HTML.
Well, this is not the case.

[Hiccup][hiccup] is a library designed to transform Clojure code into HTML
text. For example, I can create an `h1` tag using the `html` function:

````clojure
user=> (html [:h1 "Hello World!"])
"<h1>Hello World!</h1>"
````

Really I can generate _any_ HTML tag with this function, even custom tag
names!

````clojure
user=> (html [:this-is-definitely-not-a-native-html-tag-but-okay])
"<this-is-definitely-not-a-native-html-tag-but-okay></this-is-definitely-not-a-native-html-tag-but-okay>"
````

Okay so we can generate HTML with Hiccup, but how can we serve this HTML
to clients? Well, there are a few different ways to do this. 

### Keeping your Compojure

[Compojure][compojure] is one of many methods of spinning up a server.
To see how it works, just type this in the console and open your browser 
to localhost:3000.

````
lein new compojure my-server
cd my-server
lein ring server
````

If you open up `handler.clj`, you can see where the magic happens. 
You should see a `GET` method for the `/` route that just returns some 
text, as well as a `not-found` path that also just returns some text.
This is where URI paths are configured for you web server.

This is where we can start taking advantage of Hiccup's awesome library!
Let's pull out the home page and make a few updates...

````clojure
(def home
  (html 
    [:body {:style "text-align: center"}
     [:h1 "Home"]
     [:p "Hello, world!"]]))
````

While we're at it, we can also create an about page.

````clojure
(def about
  (html
    [:body {:style "text-align: center"}
     [:h1 "About"]
     [:p "Hey there, my name is Brandon!"]
     [:p (link-to "/" "Click Here") " for the Homepage"]]))
````

Now if we want to serve these pages on different routes, we
just need to add them as routes in the handler.

````clojure
(defroutes app-routes
  (GET "/" [] home)
  (GET "/about" [] about)
  (route/not-found "Not Found"))
````

Now if we run the project, we can navigate to `/` and `/about`
and check out the HTML that Hiccup generated!

[http-kit]: https://github.com/http-kit/http-kit
[compojure]: https://github.com/weavejester/compojure
[hiccup]: https://github.com/weavejester/hiccup