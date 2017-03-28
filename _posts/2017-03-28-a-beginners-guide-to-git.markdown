---
layout: post
title:  "A Beginners Guide to Git"
date:   2017-03-28 08:00:00 -0400
categories: Git
---
### Don't Panic! It's Confusing for Everyone
Git is a powerful VCS (Version Control System) that is the industry standard for tracking any and all of the changes that take place on a project. It's utility comes from it's ability to allow team leaders to conveniently manage multiple members contributions and incorporate them into a finished project. Unfortunately, it seems to be designed from the point of view of those team leaders and things can get pretty confusing if you're one of the people doing the contributing.

I know when I first started using git, I had difficulty following exactly what happened when I typed something into my terminal. I didn't understand why I had to `git add` before I `git commit`ed or what the difference between my upstream and origin were. Why was it that when I would `git pull` after having added something to staging would I get an error? What was the difference between `pull` and `fetch`? None of it made any sense and I just tried to follow the workflow that had been outlined, completely unable to troubleshoot.

GitHub likes to think that the average git workflow will end up looking something like this:
![Branching](/assets/images/branching.png)
That may be the case if working with only one or two other people on a small project. However, once you get to a corporate level, working in conjunction with hundreds of other people, things start to look a little more like this:
![Development Repos](/assets/images/Development-Repos.jpg)
While there's no arguing the necessity of such a complex Development - QA - Production environment, it's downright intimidating when you can't even follow what's happening on your own machine!

### It's Where You Work
<img alt="Git Workspace" src="/assets/images/git-workspace.png" style="float: left;">

In order to understand everything that goes on behind the scenes of the git commands you type in your CLI, you first need to understand what the scenes themselves are. On your machine, there are several different areas of memory that git will operate within. First, let's take a look at the one your probably most familiar with: Your **_workspace_**.

{% highlight sh %}
[timestmp] code
// ♥ mkdir git-practice
[timestmp] code
// ♥ cd git-practice/
[timestmp] git-practice
// ♥ git init
#=> Initialized empty Git repository in /Users/flatironschool/Development/code/git-practice/.git/
[timestmp] git-practice
// ♥ ls -a
#=> .	..	.git
{% endhighlight %}

You'll notice that this will initialize a **_local repo_** (repository) _inside_ of the **_workspace_** in a `.git` folder. A similar process happens when you `git clone git@github.com:username/projectname.git`, the only difference being that you will end up with the repo you've cloned in your **_workspace_** as well. This distinction took me some time to realize. Your **_workspace_** is not the same as your **_local repo_**. This means that when you `git push`, your **_workspace_** isn't necessarily the code that you are pushing.

The **_workspace_** is the place where you'll be doing all of your work. You should feel free to hack and slash apart the code here to your hearts content. If at any point you think you need to undo some of your changes, you can simply `git checkout HEAD` or `git reset --hard` (they do the same thing) and that will change your **_workspace_** back to what is stored in your **_local repo_**.

### In what?
<img alt="Git Index" src="/assets/images/git-index.png" style="float: left;">

Next is probably the most confusing part of your local git environment, your **_index_** (or **_stage_**, or **_cache_**). This is where the files you are about to commit go. Whenever you `git add`, the **_index_** gets populated with whichever files you are adding. Git will use a variety of different words to tell you this (because consistency would be too simple). Below, you can see them refer to the **_index_** in the line `(use "git rm --cached <file>..." to unstage)`. In this example, both `cached` and `unstage` are describing different actions being done on the **_index_**.

{% highlight sh %}
[timestmp] git-practice
// ♥ touch practice_file
[timestmp] git-practice
// ♥ touch deletable_file
[timestmp] git-practice
// ♥ git status
#=> On branch master
#=>
#=> Initial commit
#=>
#=> Untracked files:
#=>   (use "git add <file>..." to include in what will be committed)
#=>
#=> 	deletable_file
#=> 	practice_file
#=>
#=> nothing added to commit but untracked files present (use "git add" to track)
// ♥ git add .
[timestmp] git-practice
// ♥ git status
#=> On branch master
#=>
#=> Initial commit
#=>
#=> Changes to be committed:
#=>   (use "git rm --cached <file>..." to unstage)
#=>
#=> 	new file:   deletable_file
#=> 	new file:   practice_file
{% endhighlight %}

Note that depending on which version of git you are using, `git add .` doesn't necessarily add all of your changes. You should always do `git status` between your steps, but if you want to be extra sure `git add -A` will take care of that for you.

{% highlight sh %}
[timestmp] git-practice
// ♥ git commit
#=> [master (root-commit) bfbd8a2] My initial commit
#=>  2 files changed, 0 insertions(+), 0 deletions(-)
#=>  create mode 100644 deletable_file
#=>  create mode 100644 practice_file
{% endhighlight %}

At any point you can view the history of your repo with `git log`.

{% highlight sh %}
[timestmp] (master) git-practice
// ♥ git log
#=> bfbd8a2 My initial commit (szrharrison, <time> ago)
{% endhighlight %}

We can see that, now that we've committed, our **_index_** is empty

{% highlight sh %}
[timestmp] (master) git-practice
// ♥ git status
#=> On branch master
#=> nothing to commit, working tree clean
{% endhighlight %}

Note that `git checkout` will first look in your **_index_**, and then in your **_local repo_** to get the file you are checking out.

### Going Local

<img alt="Git Local Repo" src="/assets/images/git-local-repo.png" style="float: left;">

This brings us to the last key element of your local git environment: your **_local repo_**. Your **_local repo_** is your personal source of truth for the project. Any code that lives here should be ready to be shown to the rest of your team. In order to get any code onto your **_local repo_** you need to have written a commit message, so hopefully you were sure that it was a change you wanted to make. In case you change your mind and want to revert back to a previous commit, you can always `git reset --hard <commit hash>`. Note that this is a very powerful command and should only be used for commits that have yet to be pushed to the **_remote repository_** since you are essentially rewriting history. For an overall view of your local git environment, take a look at the image below (I didn't cover the stash in this post because it's not something I see used very often):

![Git Transport](/assets/images/git-transport.png)

And for tips on when and how to beautify your commit history (required sometimes when committing to an open source project) take a look at this image.

![Git Pretty](/assets/images/git-pretty.png)
