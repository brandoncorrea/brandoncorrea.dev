---
layout: post
title:  "Environmental Variables Matter"
date:   2022-03-10 7:52:05 -0400
categories: apprenticeship
---

Ah, I can finally take my shoes off... You know that feeling when you
get home after a long day and your feet just feel so stiff from 
being stuck in a shoe for hours on end? It's quite relieving, really.

Maybe you get a similar feeling when you finally resolve an elusive bug 
that's been bothering you for hours, maybe even days on end. The feeling 
of relief, finally fixed!

Today I was working on a bug causing issues with the stylesheet after 
printing... specifically in Chrome... more specifically, on an Apple machine.
What would happen is after you pressed Command + P, the page would have a 
virtually ***infinite*** height! So big that the inspector had to use scientific
notation to represent it.

It's been something to do in the backlog for a while, and I've been hoping 
it would just fix itself. Nope! Not a chance. So today I decided to tackle
this bug–toying around with stylesheets and the DOM to see what would trigger it. 

I worked with a couple coworkers to see if they had any ideas, and we came to 
a couple solutions. However, with each hole we patched, three more bigger ones
appeared elsewhere. It was a chronic illness (Apple Chrome, that is)–a losing
battle where band-aids had reached their limit.

Just when we had reached our end, I noticed a glowing button at the top right
of the browser ***"Update"***. Chrome hadn't been updated for a couple of weeks, 
but this couldn't possibly be the cause... could it? I decide to update the browser,
not expecting the issue to resolve from it.

After the browser reloads, I go the local site and attempt to reproduce the bug.
It's fixed! Hours were spent and wasted trying to find the cause of this issue, 
and it turns out that updating Chrome resolves it.

So if there's anything I took away from this, it's this:
1. I do not like Chrome (but that's probably only because I use it so much)
2. Environmental variables are important

When testing an application, you want to test it in an environment that the 
end user will most likely be using. For Apple or Windows, use the latest 
operating system. In this case, install the latest browser and test it on
that. It may just end up saving you several hours of headache!