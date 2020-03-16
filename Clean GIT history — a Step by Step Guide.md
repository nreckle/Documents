# Clean GIT history — a Step by Step Guide

[![Catalina Turlea](https://miro.medium.com/fit/c/96/96/1*It9zjT8R2Hb8FOQuTMK4-A.jpeg)](https://medium.com/@catalinaturlea?source=post_page-----eefc0ad8696d----------------------)

[Catalina Turlea](https://medium.com/@catalinaturlea?source=post_page-----eefc0ad8696d----------------------)Follow

[Jun 12, 2018](https://medium.com/@catalinaturlea/clean-git-history-a-step-by-step-guide-eefc0ad8696d?source=post_page-----eefc0ad8696d----------------------) · 10 min read











# Do you have a readable GIT commit history?

It might seem like there are few reasons to care about your git history. It seemed the same to me when I first had to start caring about it, because someone made me do it 🤓

As with everything, there will be a point where you will need to have a clear understanding of your own and your team’s changes, their succession and have to find out something meaningful from all this information — either when did a particular file got changed, which version included what changes, who did which change in what order.

Taking care before this critical situation arises that you have the cleanest state possible will prove to be very important and time saving. You will be able to use your commit history as a human readable log of atomic changes which will make it an important tool in your debugging arsenal.

In this article you can find a step-by-step guide how to move away from difficult to understand clean up your git commit history.

------

![Which side looks more like your project? Which one are you able to read and understand more easily?](https://miro.medium.com/max/60/1*4fs-40bXf4kKxzkBuHu1AA.jpeg?q=20)

![Which side looks more like your project? Which one are you able to read and understand more easily?](https://miro.medium.com/max/2048/1*4fs-40bXf4kKxzkBuHu1AA.jpeg)

> **Which side looks more like your project?**
>
> **Which one are you able to read and understand more easily?**

# First, why should you care about your git history being as readable as possible?

- Because you want to be able to easily decipher the order of the commits in your repository
- Because you want to understand more easily what and when was changed
- It facilitates finding commits that might have introduced bugs and enable rollback if necessary
- To enable automatically extracting release notes, creating a change log
- To be able to deploy any commit on your development branch using your CI/CD system
- If you are handling mobile app releases and you are responsible for figuring out what feature is in which release.

------

# How to get there?

First step you need to take is to get rid of the **merge commits**, because they make it very difficult to understand the general order of the commits and to comprehend the changes.

When you are out of date with the source branch, and you pull those changes into your branch you will get also a **merge commit**, which will be part of the merge commit when you decide to merge your changes — what a mind 🤬, right?

Speaking from experience, it is impossible to understand such a commit history, and in case of major issues, where, let’s say you would have to revert one commit, it would be very difficult to pick which commit would that be.

# First, a word of caution

Most of the operations I will present like rebase, amending commits, are **operations that change the history in git**. This means that you can break things — especially for your team mates, if you are using them (even if by mistake) on **shared branches** — branches that you share with other people in your team.

How to make sure that does not happen? — **you protect your shared branches so that nobody can force push on them.**

*Always check your commits before force pushing — I recommend SourceTree because it makes it easy to visualize changes and relationships between branches — make sure everything is there and that your project builds before force push —** if you are not sure, make a branch from the remote, to use as a backup.*

## Protect your branches in github and bitbucket

There is a setting in both github and bitbucket to protecting shared branches from having their history rewritten. Just head over to your repository settings and search for branch settings.

------

# Let’s start!

# Steps to get to a clean commit history:

- understand rebase and replace pulling remote changes with rebase to remove merge commits on your working branch
- use fast-forward or squash merging option when adding your changes to the target branch
- use atomic commits — learn how to amend, squash or restructure your commits
- learn to force push 🤓

# Let’s clean things up — initial state

![img](https://miro.medium.com/max/60/1*zgZgemcK6fhlpgUhSoXR-A.png?q=20)

![img](https://miro.medium.com/max/1068/1*zgZgemcK6fhlpgUhSoXR-A.png)

This would be the common scenario I am sure you can relate to: you are working on finishing your tasks on a separate branch while the rest of the team is doing the same — either working on their own branches or have already merged new changes into the shared branch.

# Rebasing your working branch

You already have a few commits but an important change has been made on the shared branch which you need to continue your work (or another team member worked on the same file and you have a conflict when creating the pull request).

**You have here 2 options:**

- you merge the shared branch into yours resulting in a **merge commit** which contains the changes between the remote branch and your commits since the last commit you have in common. If you have conflicts , your need to solve them and then commit.

```
git merge origin/shared_branch
```

- you rebase your commits on top of the remote branch — what this does is that it takes the latest commit on your shared_branch and then applies, one by one the commits from your working branch. If you have conflicts on any of the commits, your need to solve them and then continue the rebase.

```
git fetch
git rebase origin/shared_branch
```

The output might look something like this if you have conflicts

![img](https://miro.medium.com/max/60/1*Xkng7Kk61t9rZtj9JUbyOQ.png?q=20)

![img](https://miro.medium.com/max/2232/1*Xkng7Kk61t9rZtj9JUbyOQ.png)

```
git rebase --continue // after you have solved the merge conflicts -if any
git rebase --abort // if you want to abort the rebase and go back to the previous state.
```

If your local branch had already been pushed to remote, **you will need to force push after the rebase**. This will replace the old remote version with the newly rebased and updated one of your local branch.

```
git push -f
```

> It is very important to understand that the **force push will overwrite the remote state with the local state** — be sure to double check your changes and make sure the rebase worked well — you will not be able to revert to the previous state after you have force pushed.

## The results for the 2 operations are the following:

![img](https://miro.medium.com/max/60/1*BE81fM6aVLLhSD-6BRgLVg.png?q=20)

![img](https://miro.medium.com/max/1452/1*BE81fM6aVLLhSD-6BRgLVg.png)

Although this is a very simple example, you can definitely see that the rebased branch history is so much easier to understand, read and the relationship between the branches can be grasped in a second. The same goes for the order of the commits.

# Merging working branches into your shared branch — e.g development or master

Now you have created your pull request and the review went through and you want to merge it to the shared branch. If you are using bitbucket or github, both platforms have options when merging a PR about how the commit on the shared/target branch should look like.

![img](https://miro.medium.com/max/60/1*ZSDDiaKc_zjwoCEH4J_VXA.png?q=20)

![img](https://miro.medium.com/max/1574/1*ZSDDiaKc_zjwoCEH4J_VXA.png)

https://blog.github.com/2016-04-01-squash-your-commits/

![img](https://miro.medium.com/max/60/1*61Qkm0rtktmc4xLTNy7LZg.png?q=20)

![img](https://miro.medium.com/max/1344/1*61Qkm0rtktmc4xLTNy7LZg.png)

If you would like to do this manually, here’s the git commands for it:

```
git merge --squash working_branch
git commit -m "Squashed commit from working_branch" // meaningful commit message to be used here
```

Then the shared branch would look like this:

![img](https://miro.medium.com/max/60/1*U4RAIb93-4dXJR_1DRXOzA.png?q=20)

![img](https://miro.medium.com/max/1162/1*U4RAIb93-4dXJR_1DRXOzA.png)

# **More Strict Option: Fast Forward Merge Only**

![img](https://miro.medium.com/max/60/1*SVdEs2XMIgb00z9sMbMl7w.png?q=20)

![img](https://miro.medium.com/max/1420/1*SVdEs2XMIgb00z9sMbMl7w.png)

The bitbucket option for this flow was added just during the course of writing this article.

If your source branch has only one commit, the squash and fast forward merges will have the same result. The fast-forward merge is more strict though, your branch needs to be rebased on the shared branch before this can succeed. This is not necessary for squash, excluding the case where there are conflicts.

# Rearranging and changing your commits

One other thing you can do to make sure your git history is as meaningful as possible and it contains atomic steps in the development is to rework your commits when necessary.

⚠️**This should only happen before the PR, never on the shared branch (master or development)**

Common examples from my experience:

- you need to make small changes to the previous commit — which are not meaningful on their own — like typos, extra spaces etc.
- you realized you can reorganize your commits better to ease review and understanding for your team.
- there are conflicts to the target branch in a file/files you have modified in multiple commits on your branch — meaning you will have to solve the conflicts on every single step of the rebase. So why not make your life easier and put all those changes that might cause the conflict into one commit and only fix them once? 🤷‍♀️

Let’s go through the available options and to which cases these would be more convenient:

- **Interactive rebase** — this allows you the possibility to manipulate multiple commits at once

```
git rebase —-interactive origin/target_branch
```

Running this in the terminal will end up looking something like the following:

```
pick 84ff2985 Increased build number
pick e68de491 Increased build number
pick ef8e579d Merged in localisation-update-121 (pull request #637)
pick e5a515ac Added README file for repo
# Rebase da3dea1b..9e8abb0c onto da3dea1b (29 commands)
#
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like “squash”, but discard this commit’s log message
# x, exec = run command (the rest of the line) using shell
# d, drop = remove commit
```

You have the available commands listed above.

A possible situation for using this would be if you had addressed the comments to your PR in various commits and you would like to group them together into one.
An even more interesting situation would be when you want to change something in a commit that is not the last one — which is covered with then amend option.

- **Reverting to a previous commit and reordering the changes into one or more new commits**

```
git reset origin/shared_branch
git commit -am “All my relevant changes in one commit”
```

With this option you can completely rearrange one or more commits you have already made. You can reset to the shared branch and then commit all the changes in one big commit or in multiple smaller ones.

If you have significant changes and there are merge conflicts with the target branch, you can use this option to reduce the amount of times you need to fix the conflicts — for example, **only once instead on at every rebase step.**

- **Amending the previous commit**

```
git add . // stage your changes
git commit —-amend
```

The commit will have a new hash and if the branch had already been pushed to remote you will need to force push afterwards. This option can also be used without any changes when you want your commit to appear new — for example, to trigger some web hook in your CI.

------

# What else is important?

## Checking out branches after rebase/amend/reset operations

Let’s imagine one of your team mates rebased her branch and you would like to test the changes one more time. If you checkout the branch and try to pull you will end up with a **merge commit** which merges your local changes with the remote one — most probably also many conflicts.

This happens because, with the rebase/amend/reset operations the commit history was changed so git does not recognize your local branch previous to the operation as related to the remote branch — it will only look as 2 branches changing mainly the same files — that also where the conflicts come from.

**How to fix it?** Just reset your local branch to the remote one and everything should be ok.

```
git checkout this_other_branch
git stash // Just in case you have local changes that would be dropped if you dont stash them
git fetch
git reset —-hard origin/this_other_branch
```

> **⚠️⚠️Make sure you have no local un-stashed changes when running the hard reset — otherwise they will be lost**

## Generating Changelog from your commit history

Using basic git commands you can read and format the commit messages so that you get a meaningful changelog — at least as meaningful as your commit messages 🤓

If you are using fastlane, it has an integrated step to generate the change log and upload it to testflight, or post it to slack or whatever might be necessary for your team.

# Thanks for reading!

## If you give this a try, I would love to hear your feedback!

As well as if you might need more info about any of the steps, how to apply them, where to start. I would be more than happy to support you 😊

# Happy committing 🤓!

------

If you would like to get **personal coaching for improving your iOS development skills** and get input from me on your most pressing iOS related issues, I am currently open to coaching other devs. If you are interested, just get in contact . More details here: https://mobiledev.consulting/1x1-expert-coaching/

***My goal is to be constantly learning and I believe there is no better way to gain deeper insights than teaching and sharing with others what you know. This is why I want to share my expertise to any mobile developer interested in learning more and getting stronger in their skills.\***