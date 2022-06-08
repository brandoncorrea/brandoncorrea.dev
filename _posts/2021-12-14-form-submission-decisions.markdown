---
layout: post
title:  "Form Submission Decisions"
date:   2021-12-14 7:52:05 -0400
categories: apprenticeship
---

I was working on a web page today that I wanted to perform several 
operations on.
1. GET - Simple HTML retrieval
2. POST - Guess a number
3. POST - Create a new guessing game

I was able to implement the GET and guess functionality, but I ran into
an issue with the new game operation. How do I signal to the server that I
want to create a new game, and then have the server refresh the page?

In a JavaScript framework I might call a completely separate API, `/newGame`
for example. After that I'd probably retrieve the new game from another API
and load that data on the page. But how can this be done completely on the
server side, without JavaScript?

This is the HTML I was trying to work with, and I needed to add a "New Game"
button somewhere on the page. But how would that button tell the server 
to create a new game?

````html
<form action="/guess" method="POST">
    <label for="number">Number: </label>
    <input type="text" id="number" name="number" value="" />
    <input type="submit" value="Guess" />
</form>
````

I could try using a different HTTP Method, but I'd really like to have 
this in a POST request, since it's changing some state of the application.

A quick Google search revealed something that should have been obvious to be,
but just went completely over my head. The solution is to just add a button
with a different `name` attribute!

````html
<form action="/guess" method="POST">
    <label for="number">Number: </label>
    <input type="text" id="number" name="number" value="" />
    <input type="submit" name="guess" value="Guess" />
    <input type="submit" name="newGame" value="New Game" />
</form>
````

When one of these buttons is clicked, a POST request will be sent to the
server with a message body that looks something like this:
- `number=4&guess=Guess`
- `number=&newGame=New+Game`

Then on the server side, I can check the request body for either the
`guess` or `newGame` flags, and decide where to go from there!
