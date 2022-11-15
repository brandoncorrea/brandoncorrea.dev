---
layout: post
title:  "Optimizing Slow Client-Server Connections"
date:   2022-11-15 7:52:05 -0400
categories: performance
---

I recently took a trip to Guatemala, which is a bit behind the United States
as far as technological infrastructure goes. 

### The Problem

Paying close attention to my network speeds, 
I noticed that while I would occasionally get LTE speeds, I would often run into areas where 
I had 3G, E, or no data connection at all.

Even at the hotel I stayed at, the Wi-Fi wasn't exactly the best. Maybe it was just
that hotel that was slow, or maybe it was the number of people that were using it.
But in any case, the fact is that I as a user had to suffer a slow internet connection.

This got me thinking about things like payload optimization, in-country web hosting, 
and even webpage internationalization to display one language or another based on
the browser preference.

So if I were to build an app or a website for users in Guatemala, how would I do this?
Let's assume the user's device is capable of handling whatever JavaScript code we give it.

### Minimizing Payloads

The first thing that comes to mind is minimizing the payload. If there isn't tons of 
data to send, then it's likely that the user will actually receive the payload in a 
reasonable amount of time.

This means all your source files need to be minified and compressed (HTML, CSS, JavaScript, 
Images, Videos, etc.) and you only send what the immediately needs first.

### Delaying Content

Normally in React, I would just send the entire website to the user and the browser would 
handle routing and page rendering.

However since we are trying to minimize the payload, we may not want to send the entire website to the user. 
Maybe adding a "More..." button that request more information once the user opens them.
Or maybe triggering requests for the rest of the content after the page loads.

There are a few different ways to do this, but the idea is to minimize the time it takes
for the first page to load. This may also mean that you do wind up sending the entire JavaScript codebase. 
Since JavaScript can render reusable components, it may wind up being a smaller payload than the raw HTML.

### Internationalization

What if I want to display the website in a different language? Spanish, German, Creole...

If my users were all in Northern American countries, then I could just send them my entire 
codebase and have the browser take care of swapping languages. The user would never know the 
difference because network speeds can generally handle that sort of payload.

But we want to minimize the payload being sent, which means we only want to send the language
requested by the user in the `Accept-Language` header.

As far as your JavaScript is concerned, you may wind up sending one of many `language.js` files with
the content for each page. So you'd have `en.js`, `es.js`, `de.js`, and so on. Based on the language header,
you'd send one of these files to the client.

### Web Hosting

One last thing we should consider when optimizing requests is where our server is located. If you
can find a reliable hosting service close to where most of your users are located, use that. It
should improve your client-server connection.

I haven't looked too much into web hosting in Guatemala, but AWS doesn't seem to have any sort 
of web hosting in Central America. The closest thing for AWS would be São Paulo, Brazil or maybe Virginia, US,
which are both quite far from Guatemala. Azure has web hosting in "South Central US", but ideally the web server 
would be hosted *in* Central America.

### Higher Quality

Now I'm a user who always has great connection speeds *and* I want the best user experience
this website has to offer. Well as of writing this, there doesn't seem to be a way to detect a client's
network speeds. So we can't guess what payload would be best for our clients–they have to tell us.

You may be familiar with a "Request Desktop Page" feature on some websites or browsers. 
While this may be slightly different than what we're trying to achieve here, we can still
use something like this to our advantage.

On first visiting the website, the app could ask the user if they want the full version
or a lite version. This setting would then be saved as some sort of cookie or browser setting
for future requests to the server. Another way this could be done is providing a setting somewhere 
on the page to swap between full and lite versions of the app. Again, setting some preference on the 
user's browser for later use.

### Conclusion

There are probably a lot of things I'm missing here–and maybe some wrong assumptions. But the problem exists: 
There are people with internet connection places like this that simply cannot open a webpage due to the 
network speed and the payload size.

Keeping the above things in mind when building an app may be beneficial in getting those people connected to
your app.
