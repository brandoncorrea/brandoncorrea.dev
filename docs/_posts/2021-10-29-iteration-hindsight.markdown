---
layout: post
title:  "Iteration Hindsight"
date:   2021-10-29 7:52:05 -0400
categories: clojure, apprenticeship
---

When planning sprints each week, we have an Iteration Planning Meeting (IPM) where we 
go over what we've done over the last sprint as well as plan out what will be done in 
the next sprint.

This week during my IPM, there were a couple iteration stories that I missed due to an 
underestimated story, which ended up taking more time than I had anticipated. This
sort of thing can happen from time to time, but shouldn't happen on a regular basis. 
That said, there are a few things I could have done to better handle the situation. 

### What Went Wrong?

The client didn't find out that two of the three stories they requested wouldn't 
be ready until the IPM–this is the worst time for the client to find out about this. 
Had the client been informed earlier in the week, even if only a day, it would 
have been a lot less impactful. 

Suppose you take your car to the mechanic on Tuesday, and he says it will be ready 
by Friday afternoon. This works great for you, since you have plans Friday evening. 
Now Friday comes around and you're ready to pick up your car, but the mechanic tells 
you that the car won't be ready until Monday. 

How would you feel in this situation? What does this tell you about the mechanic? 
You still need a vehicle this evening, but now only have a couple hours to find 
something and may even have to cancel your plans. Had the mechanic informed you even 
a day earlier, you would have had a bit more time to make arrangements. 

### Knowing When to Call It

When would have been a good time to reach out to the client? This particular iteration 
story was estimated to be about a day and a half to complete. This actually ended up 
taking about three days–twice my estimation. 

Some bells should have gone off by the second day of working on the story, "Hey, this 
story has already reached its estimation, and I'm only halfway done!" This would have 
been the best time to reach out to the client, still early in the sprint.

### Don't Buckle Down Just Yet

Try to resist the temptation of "I can finish this if I just buckle down and work through it."
After informing the client that a story is taking a bit long, they may let you know to 
prioritize another story in the iteration, and come back to that one if time permits. 

This helps you in two ways:
1. Lowering the client's expectation before IPM.
   - "I'm missing story X this week, but can still expect Y and Z."
2. Surprising the customer at IPM, in a good way.
   - If it turns out you are able to finish all the stories, 
   the customer will be ecstatic during the IPM.
   - "Wow! I thought I'd only get Y and Z this round, but X was delivered too!" :D

### Communication

Communication is key when working on a software project. In the [Agile Manifesto][agile-manifesto],
One of the four values is "Customer Collaboration over Contract Negotiation." 

The iteration stories and estimates are the contract that we are agreeing upon with the customer. 
When we cannot satisfy that contract for one reason or another, we must _collaborate_ with the 
customer and see what would be best for the _customer's competitive advantage_.

[agile-manifesto]: https://agilemanifesto.org