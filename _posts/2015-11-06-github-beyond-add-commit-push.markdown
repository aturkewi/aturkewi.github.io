---
layout: post
title: "Git: Beyond Add, Commit, and Push"
modified:
categories: [git, git hub]
description: Looking at some git commands beyond the basics.
tags: []
image:
  feature: git_cover_photo.jpg
  credit:
  creditlink:
comments:
share:
date: 2015-11-06T13:35:19-05:00
---
It seems that most people know the basics of github if you've been using it for a few weeks:

- `git add .` - Stage all files that are in your current directy level and lower.
- `get commit -m "Some note"` - Commit staged changes with some note.
- `git push remote_name branch_name` - Push changes from a specific branch to some previoulsy named remote site.
- `git fetch remote_name branch_name` - Download a specified branch from remote site.
- `git merge branch_bame` - Combine code from specified local branch to branch that you are currently on.
- `git pull remote_name branch_name` - FETCHES and MERGES remote branch into current local branch.

These commands are great to know but they aren't the most helpful when things start going wrong:

<figure align='center'>
<img src="http://imgs.xkcd.com/comics/git.png" title="If that doesn't fix it, git.txt contains the phone number of a friend of mine who understands git. Just wait through a few minutes of 'It's really pretty simple, just think of branches as...' and eventually you'll learn the commands that will fix everything." alt_text="XKCD-1597"><br>
<figcap align='center'>http://xkcd.com/1597/</figcap>
</figure>

And an important part of being a developer is breaking things and learning to fix them. Ocassioanly we'll break something very badly and need to back track a little to put it back together. This is an instance where knowing a little bit more about git can really help. Knowing what is saved, when it gets saved, how it gets saved, and how to use this information can be a powerful confidence builder. My knowledge of git has helped me feel free to break things knowing that I can at least always get back to where I started.

##When is my code saved?

One of the first things really confused me is 'When does git save my code?'. I would make a new branch, start working and then realize that I hadn't actually switched to the new branch yet. Was my work saved to the master branch? Sometimes I would be working on one branch and that work would show up on ALL the branches.

Here is a quick example to demonstrate WHEN your code is actaully saved to a branch.

1 - Here I am in a fresh repo.

<div align='center'>
<img src="/images/2015-11-06-git-hub/img1-new-repo.png" title="New repo" alt_text="New repo">
</div>

2 - I add a new file while on the master branch.

<div align='center'>
<img src="/images/2015-11-06-git-hub/img2-new-file.png" title="New file" alt_text="New file">
</div>

3 - Now I create a new branch called 'new-branch' and switch to it. I want to check and see if the file comes with me. After all, I made it while I was on the master branch, so maybe it should stay where it is.

<div align='center'>
<img src="/images/2015-11-06-git-hub/img3-new-branch.png" title="New branch" alt_text="New branch">
</div>

After switching to new-branch, I can see that my new file is showing up here as well. Git knows that it's a new file and I can plainly see it in the directory. Let's stage this file now by using `git add .`.

4 - Well, now that I staged (added) this file, surely it should stay on new-branch, right? Let's check.

<div align='center'>
<img src="/images/2015-11-06-git-hub/img4-commit-to-master.png" title="Switch back to master" alt_text="Switch back to master">
</div>

Well, it looks like adding the file didn't stop it from moving around when I switched branches either. Now I'll commit the file to the master branch.

5 - Just to check, lets make sure it's not still showing up on new-branch.

<div align='center'>
<img src="/images/2015-11-06-git-hub/img5-check-branch.png" title="Verify that file is not on new-branch" alt_text="Verify that file is not on new-branch">
</div>

GOOD! The file is finally acting as I suspected.

This shows that no matter what branch you were on when you created a file, made changes to a file, or even stage something, it does not become attached to a branch until you commit the changes.

## Where does my new-file go when I leave the master branch?

Good question. They are actually stored in the directory .git. Go take a look at some of those previous screen shots. When I list the contents of my project folder, there is always a .git file. All the changes that I make between branches are stored using a 40 character long hex hash. Take a look at git's files for each branch.

<div align='center'>
<img src="/images/2015-11-06-git-hub/img6-master-commits.png" title="Show hash for master commits." alt_text="Show hash for master commits.">
</div>

And now lets take a look at the file for my new-branch

<div align='center'>
<img src="/images/2015-11-06-git-hub/img7-new-branch-commits.png" title="New branch commits." alt_text="New branch commits.">
</div>

We can see each hash associated with each commit on each branch.

Some other interesting information to take a look at here is the config file.

<div align='center'>
<img src="/images/2015-11-06-git-hub/img7a-config.png" title="Config file" alt_text="Config file">
</div>

Here in the config file you can see your remote url as well as a few other bits of information. If you ever find that you need to change your remote name or URL, you can always just open up this config file and manually change it.

So far I haven't gone over any 'complex' git commands, but its all useful information to know to better understand the tool we are working with. The more you understand about it, the more comfortable you can be with it. But still, there are some more useful commands to know beyond `commit`, `add`, and `push`.

## What to do if you've made a mistake

One mistake that I sometimes make is commiting to a branch when I did not mean to.

<div align='center'>
<img src="/images/2015-11-06-git-hub/img8-commit-file-to-master.png" title="Accidentaly commited a file to master." alt_text="Accidentaly commited a file to master.">
</div>

A good thing to remember is that you can ALWAYS roll back with git, that's the whole point. So in this case, I just made an empty file on the wrong branch and I want to delete it. First, take a look at `git log` to see how far back I want to go and get the commit number (the seven digit number to the left of the commit message). Then use the command `git reset --hard [commit number]`. This will roll back your current branch to that specific commit.

<div align='center'>
<img src="/images/2015-11-06-git-hub/img9-git-reset.png" title="Reset to previous commit." alt_text="Reset to previous commit.">
</div>

Now we can see that the file we created no longer exists!

## A few more useful tools

Some ohter useful commands that I've come across are `git log --oneline --decorate --graph --all` and `git merge --abort`. The flags attached to the git log allow you to see a quick and nice-ish visual of what all the branches look like for your project.

<div align='center'>
<img src="/images/2015-11-06-git-hub/img10-tree.png" title="Terminal rendering of you git tree." alt_text="Terminal rendering of you git tree.">
</div>

This is rendered in the terminal after all, but it's not a bad representation.

And finally `git merge --abort`. This command will revert to a pre-merge state (exactly what you would think it would do). It's greate if you merge a branch that wasn't ready or all of a sudden see a whole bunch of merge conflits that will need further follow up.

There are many more git commands and tools out there beyond these meager few. If you think there might be something that git can do to help you out, search for it. The more comfortable you feel with git, the more comfortable you will feel with programing.

## Quick Flatiron Tip
One more quick tip for my Flatiron School class. If you're looking to improve your speed in working with git, try typing `alias` into your console. You get a whole bunch of pre-loaded github shortcuts. These are all stored in your .bash_profile in your root directory and you can easily create custom ones for yourself by simply copying the syntax.

<div align='center'>
<img src="/images/2015-11-06-git-hub/img11-git-shortcuts.png" title=".bash_profile alias" alt_text=".bash_profile alias">
</div>

Sources:
https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell
source: http://www.javaworld.com/article/2113465/developer-tools-ide/git-smart-20-essential-tips-for-git-and-github-users.html
