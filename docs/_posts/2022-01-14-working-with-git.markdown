---
layout: post
title:  "Working with Git"
date:   2022-01-14 7:52:05 -0400
categories: git, apprenticeship
---

Working on a team project has gotten me a lot more familiar with git,
having to create multiple branches for different features, pushing to the
remote repository, and merging branches into one another.

Let's take a look at a few commands in a fresh Clojure project!

````console
> lein new speclj bowling-game
> cd bowling-game
````

### git init

`git init` will initialize your git repository. 

````console
> git init
hint: Using 'master' as the name for the initial branch. This default branch name
...
Initialized empty Git repository in /Projects/bowling-game/.git/
````

Now we have a new local git repository for our Bowling Game project!

### git status

`git status` is probably my most used command. This tells me where I'm
at currently in my local git session. Let's see what this gives us after
we've just run `git init`.

````console
> git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	.gitignore
	README.md
	project.clj
	spec/
	src/

nothing added to commit but untracked files present (use "git add" to track)
````

At the top, we see we are on the master branch and a bit below that we have
some files that haven't been staged for committed yet. 

### git add

Since we've just created this repository, let's go ahead and stage these 
files for a commit. As shown in the `git status` output, you can stage one 
file at a time with `git add <file>...`.

````console
> git add README.md
> git add project.clj
> git add src/bowling_game/core.clj
> git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
	new file:   README.md
	new file:   project.clj
	new file:   src/bowling_game/core.clj

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	.gitignore
	spec/
````

At this point, we could "commit" our staged files, and only those three 
files would be saved to master branch. But since this is our first commit,
we want to commit all of our files. While we could use the `git add <file>...`
command, there's a much quicker way to stage all your untracked files: `git add .`.

````console
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
	new file:   .gitignore
	new file:   README.md
	new file:   project.clj
	new file:   spec/bowling_game/core_spec.clj
	new file:   src/bowling_game/core.clj
````

### git commit

Now that all our files are staged, we're ready to commit! The easiest way
to commit is to run `git commit -m <commit message>`.

````console
> git commit -m "First commit"
[master (root-commit) 131d6ff] Initial commit
 5 files changed, 36 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 README.md
 create mode 100644 project.clj
 create mode 100644 spec/bowling_game/core_spec.clj
 create mode 100644 src/bowling_game/core.clj
````

If you check your git status, you'll see that there's nothing to commit.
All changes have been saved in your git repository on the `master` branch.

````console
> git status
On branch master
nothing to commit, working tree clean
````

Now, occasionally you may accidentally type `git commit` without the `-m` 
argument. This will put you in the `vim` editor, and if you're as unfamiliar
with it as I am, you may get a little lost. So here's what you can do if you
encounter this scenario.

````console
> git commit
# Please enter the commit message for your changes. Lines starting
# ... more comments ...
````

Press the `I` key to start typing, then type your commit message.

````console
First commit
# Please enter the commit message for your changes. Lines starting
# ...
````

After you've finished typing your commit message, go ahead and press `ESC`,
type `:wq`, and press `ENTER`. After that, you should see the commit
output in your console!

### Branching Out

There are plenty of reasons to have different branches in your git 
repository. You may be working with several other people, or maybe you have
a few different features that you want to keep separated until complete.
Whatever your reason, here are some ways you can create branches.

````console
> git checkout -b feature/spare
Switched to a new branch 'feature/spare'
````

`git checkout -b <branch>` will create and switch to a new branch.
If we enter `git branch`, we can see all available branches and which one
we currently have checked out indicated by the asterisk.

````console
> git branch
* feature/spare
  master
````

If we check our `git status`, we can see we are indeed on feature/spare
and that the branch is clean (nothing modified from the last commit).

````console
On branch feature/spare
nothing to commit, working tree clean
````

Go ahead and modify a couple files. You could just add a comment anywhere
in `spec/bowling_game/core_spec.clj` and `src/bowling_game/core.clj`.

````clojure
; Updated from feature/spare
````

Now if we check our git status, we will see those changes being tracked.

````console
git status
On branch feature/spare
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   spec/bowling_game/core_spec.clj
	modified:   src/bowling_game/core.clj

no changes added to commit (use "git add" and/or "git commit -a")
````

### git stash

Now I need to go back to `master` for something, but I'm not quite ready 
to commit my changes. I really don't want to lose all the work I put 
into this branch, so I need a way to save the changes without committing them.
Well, `git stash` will do exactly that!

````console
> git stash
Saved working directory and index state WIP on spare: 131d6ff Initial commit
````

If I enter `git stash list`, I can see my stash is saved in `stash@{0}`.

````console
> git stash list
stash@{0}: WIP on spare: 131d6ff Initial commit
````

All your stashes are saved in a stack-like list. So the latest stash will
always be saved in `stash@{0}` while older stashes will be toward the bottom
of the list.

### git stash apply

I actually don't need to go to master anymore, so I'll need to reapply the 
changes I just stashed. There are a few different ways to do this.

`git stash apply stash@{N}`, where N is the stash number, will apply the 
specified stash to the current branch.

````console
> git stash list
stash@{0}: ...
stash@{1}: ...
stash@{2}: ...
stash@{3}: WIP on spare: 131d6ff Initial commit

> git stash apply stash@{2}
On branch feature/spare
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   spec/bowling_game/core_spec.clj
	modified:   src/bowling_game/core.clj

no changes added to commit (use "git add" and/or "git commit -a")
````

A couple other ways to apply stashes:
- `git stash apply`
  - This is synonymous to `git stash apply stash@{0}`
- `git stash pop` 
  - This will apply changes from `stash@{0}` and then delete that stash

### git switch

Okay, now I actually do want to go to `master`. Let's go ahead and 
commit our changes to `feature/spare` and switch over to `master`.
This is actually a pretty simple command. Just type 
`git switch <branch>`–Not too complicated.

````console
> git add .
> git commit -m "Implement spare"
[feature/spare 8192308] Implement spare
 2 files changed, 2 insertions(+), 2 deletions(-)
> git switch master
Switched to branch 'master'
````

Now if you go to your `core.clj` and `core_spec.clj` files, you will see
that your changes are no longer there. All those changes were left
in your `feature/spare` branch. 

If you want to bring those changes back over to `master`, you can enter
`git merge feature/spare` and you'll see those changes come back on the master
branch!

There's still tons of other commands and options that you could go through
with git, but these are just a few that I've found most useful over the past
few days.