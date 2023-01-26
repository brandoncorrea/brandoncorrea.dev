---
layout: post
title:  "A Value-Driven Approach to Stories"
date:   2023-01-25 7:52:05 -0400
categories: craftsmanship
---

My approach to implementing stories has changed over time.
I'd like to describe each point in this progression as well
as where I finally landed on my approach to stories.

### One Branch per Story

On some of my very first stories, I would create a new branch, 
implement the story on that branch, switch back to master, 
create a new branch, and repeat for all subsequent stories.

This approach was incredibly inefficient. There was code 
I would write on one branch that I would inevitably need on
another. This meant either swapping through my branches to
find that code, or rewriting the same code I just wrote. 
_Awful!_

As far as the merge conflicts I would run into (with myself)... 
well, I'll just let your imagination run free there.

### Branching then Merging

The branch-per-story approach didn't last long. After that, 
I would still create a new branch for each story, but then
merge with master after that story was complete and create
new branches off that.

I stuck with this approach for quite a while before
refining it further.

### Unfailing Commits

Over time, I got into the habit of never committing failing 
code–even if only locally. I started treating every commit 
as if it were a push to master. _Will this break things?_

There are several natural benefits with this approach:
1. I don't wind up accidentally pushing bugs to master
2. I can `git push` to master at any point in time
3. I can safely `git checkout` any commit and still be passing
4. I can work on multiple stories without having to swap between branches
5. I can commit directly to master (because every commit passes)

No more branching out and no more sifting through
merge conflicts! Well... kinda.

### Frequently Integrating

I say no more merge conflicts, but what I mean is 
significantly fewer conflicts. There were still cases
where I would implement a larger feature, commit, then 
`git pull --rebase` and still wind up having to resolve
merge conflicts.

So since we are in the habit of committing only passing code,
let's just create a new habit of committing more frequently. 
How do we do this?

#### Breaking Down Stories

Just about every story can be broken down into smaller
bite-sized components.

For example, I need to build a webpage that allows users
to manage inventory. This can be broken down into at least
five different components:
- Viewing Items
- Creating Items
- Updating Items
- Deleting Items
- Creating the Static Webpage

Now that we have our bite-sized chunks, we just need to
work on and commit each component separately, rebase 
with origin, and push that passing code to master.

This approach ensures [continuous integration][continuous-integration]. 
You always have your team's latest changes and 
your team always has your latest changes.

### Providing Value in Every Commit

Here we are. The cherry on top. The final iteration (so-far)
of my approach to stories.

Allow me to begin this section by pasting in several 
of the twelve principles of the [Agile Manifesto][agile-manifesto]:

    Our highest priority is to satisfy the customer
    through early and continuous delivery
    of valuable software.

    Deliver working software frequently, from a
    couple of weeks to a couple of months, with a
    preference to the shorter timescale.

    Working software is the primary measure of progress.

    Simplicity--the art of maximizing the amount
    of work not done--is essential.

The focus here is to deliver valuable, working software,
soon and simply.

#### Prioritizing Customer-Facing Components

We're already familiar with partitioning our stories 
into smaller components. Now let's prioritize these 
components in order of what delivers the most value 
to the customer:
1. Creating the Static Page and Route
2. Viewing Items
3. Creating Items
4. Deleting Items
5. Updating Items

You may be wondering why a static page is the most valuable
piece to this story. Let's talk about that real quick.

#### Wow-ing Your Customer With Early Delivery

Remember, our goal is to deliver valuable and working 
software early, frequently, and simply.

The customer typically doesn't care about how a thing is implemented.
All the customer cares about is that the interface you 
provide them gives them the feedback they expect, because
that is the only part of the app they will ever use.

Suppose you implemented these features backwards, from 5 to 1,
and pushed to production with each commit. The customer 
wouldn't notice anything until the final push because 
nothing would be usable until _all_ the components had
been implemented.

Now consider this: What would happen if you were to push 
each of these components to production in sequence?
The customer would see a new feature with each push!
1. Wow! A new (blank) page for my items!
2. Wow! All my stuff is here!
3. Wow! I can create new items!
4. Wow! Now I can delete things I no longer need!
5. Wow! Now I can fix my mistakes!

Now with every commit, we're not only providing a passing 
component, but adding value to the customer's product. 
With this approach, you'd be encouraged to push to
production throughout the day because each commit provides _value._

#### Delaying Decisions

Delaying decisions is a key factor in keeping your code simple
_(maximizing the work not done)._ However, this doesn't just
apply to big decisions like infrastructure or which database to
use. We can do this even in smaller the details of our stories!

Starting with the UI means delaying decisions made on the backend.
If the frontend doesn't need it, the customer will never see it,
and the backend doesn't need it either!

Remember, customers don't care how a thing is
implemented–the UI _is_ the app. So if the interface can
trick the customer into thinking the app is working,
then the app is effectively working.

#### Write No Code

"Uncle Bob" has described [three rules of TDD][rules-of-tdd] in the past:
1. You are not allowed to write any production code unless it is to make a failing unit test pass.
2. You are not allowed to write any more of a unit test than is sufficient to fail; and compilation failures are failures.
3. You are not allowed to write any more production code than is sufficient to pass the one failing unit test.

We can re-word rules 1 and 3 a little to fit our approach to stories:
1. You are not allowed to write any production code unless it is to _satisfy a story_.
2. You are not allowed to write any more production code than is sufficient to _satisfy a story_.

One of the best ways I've found in keeping things simple is to
write no code until absolutely necessary. If it doesn't add value,
the code serves no purpose but to complicate the existing codebase.

In TDD, your first and simplest test is a no-op: "It does nothing", "It returns nil".
In a Value-Driven approach, the simplest code you can write is
a blank file... but even that's too complex! The file adds no value–delete it.

To get into the right mindset, I've attributed this sort of malevolent 
characteristic to code: That every line of code written is a sin against 
my other sins (code). In most cases, these are necessary sins, and we must do our 
best to minimize and simplify these sins.

### Summary

Over each iteration of this development approach, I've 
learned something new:
1. Branches foster merge conflicts
2. Frequent and passing commits are integral for productivity
3. Customer-Facing components provide the most value
4. Starting with the UI helps to simplify the backend work

If you were to leave here with anything, leave with this:
1. Commit small components of stories
2. Only commit passing code
3. Integrate with master throughout the day
4. Start with components that provide _value_ to the customer
5. Write no code until absolutely necessary

At the end of the day, our priority is to deliver
valuable software to the customer. These practices have
helped me manage this in even the minute details of my
commits. I hope this can help you too.

[agile-manifesto]: https://agilemanifesto.org/principles.html
[continuous-integration]: https://en.wikipedia.org//wiki/Continuous_integration
[rules-of-tdd]: http://butunclebob.com/ArticleS.UncleBob.TheThreeRulesOfTdd