---
layout: post
title:  "Frequently Merging"
date:   2022-01-26 7:52:05 -0400
categories: apprenticeship
---

Working on a team project is vastly different from working on your own
projects, and one of these differences is having to manage several
version of the same product. With all these versions floating around, 
they will eventually need to be merged into the "master" branch. 
This can either be a very simple or very painful process.

### Merging infrequently is time-consuming

When you're working on a project, how many changes do you make in a single
week? How does the file structure change for one person's tasks alone?
Now multiply this by the number of people working on the project.

If everybody merged their code at the end of a one or two week 
iteration, how much more time would you spend resolving all the conflicts 
between each person's branch? How much time do you spend testing that
newly merged code, ensuring that one change didn't break another?

### Unmerged code is not deliverable

At the end of the iteration, you should be ready to deliver _some_ new
changes to an application. But if all these features are in different 
branches, can you really deliver them? If the client wants you to deploy 
the latest changes, could you just hit that green button or would you need
to merge everything together, test-drive it, _and then_ deploy?

If you run into issues when merging, what do you do? The client is waiting 
to see the new changes they paid for in production–where are they?

### How often should I merge?

The first indicator for me that I'm ready to merge is when I complete a
story or implement a new feature. This is a working deliverable, and can 
be deployed safely. Another indicator that I'm ready to merge is when 
I have _part_ of a story complete, but that part does not break anything–it
can be delivered without the rest of the story.

What if you're working on a large feature that's spanning the entire 
iteration, or maybe even multiple iterations? Stories like this can _usually_
be broken down into smaller pieces, but let's say it was agreed that this 
single feature would take two weeks to complete. What does the process of 
merging your code on a daily basis look like?

If you're spending two weeks on this, you are _probably_ writing a few 
hundred lines of code. Which means this feature should _probably_ be 
written in its own class, namespace, or module. Now you have free rein
over what happens in this set of files and can merge your code as you 
complete small chunks of the story.

### Conclusion

Merging on a daily basis saves time and keeps the project moving. 
It allows everyone to code around the latest set of features, rather than
stepping on toes that they didn't know were there. This puts multiple sets 
of eyes on each person's code and if there's a bug or recommendation 
for a feature, it can be addressed sooner rather than later.
