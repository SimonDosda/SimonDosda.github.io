---
layout: post
title:  Git Aliases for an Efficient Workflow
date:   2021-04-13
cover_image:  2021-04-13-feature.png
author: Simon Dosda
categories: git tool
---

Git is one of the most important tools for any kind of development, the first thing anyone wanting to start a coding journey should learn, as emphasized in the [New Developer? You should’ve learned Git yesterday](https://codeburst.io/number-one-piece-of-advice-for-new-developers-ddd08abc8bfa){:target="_blank"} article from *Brandon Morelli*.
In this article, I am not going to talk about what git is or why it should be used, but how you can be more efficient using it thanks to aliases. 

In the following sections, I go through the details of all my aliases, why and how I use them, but if you are in a hurry you can go directly to the [last section](#summary) to have the complete list of them.

## Basic Shortcuts

The first step of laziness. These aliases will just shorten the number of characters you type. The one I use the most is `co` for `checkout`. I have defined other ones but don't really use them ; I prefer their variants which I will present later. Meanwhile, here is a list of classic aliases that I configured.

```bash
co = checkout
br = branch
ci = commit
st = status
```

More interestingly, you can also use this kind of shortcut to create new commands. The two ones I use a lot are for branch management.

```bash
nb = checkout -b  # create a new branch
del = branch -D  # delete a branch
```

These allow you to create a new branch by typing `git nb <branch-name>` (`git chekcout -b <branch-name>` never felt natural to me) or to delete one by typing `git del <branch-name>`.

## Enhanced Shortcuts

I personally find some default behaviors of git quite annoying. 
For instance, the fact that you need to set the name of the remote branch when you first push it, whereas I think I never came across the situation where I wanter this name to be different from the one of the local branch.

To solve this I use an alias, `git p`, that pushes the branch while setting the upstream branch with the same name.

```bash
# push to the same branch name
p = !git push -u origin $(git rev-parse --abbrev-ref HEAD)
```

Most of the time I also want to stage all my modifications, and of course add a message to my commit, so I have an alias allowing me to do this all at once: `git c "<my-commit-message>"`. If I just want to commit a work in progress, I do `git wip`, and it commits everything with WIP as a message.

```bash
# stage and commit all the changes
c = !git add -A && git commit -m 
# stage and commit all the changes with WIP as a commit message
wip = !git add -A && git commit -m 'WIP'
```

Finally, there is no quick way to get rid of everything we have done. However, sometimes we just play around a bit with no compelling results. To do so I have a `git drop` alias. There are several ways to do this, in my case I just stash my changes and drop them.

```bash
drop = !git stash && git stash drop  # drop current changes
```
## Status helpers

I have a couple of aliases to give me some information about the current state of my repository. The first one is `git state`, which will fetch the remote repository (while removing deleted distant branches and fetching tags, some other default behaviors of git I don't understand), and then will display the branches status locally and on the server using the verbose mode.

The other, a simpler one, is `git ll <n>`. It lists the n last commits of the current branch on one line.

```bash
# give a quick look at the state of the repo
state = !git fetch --prune && git fetch --tags && clear && git branch -vv && git status
ll = !git log --oneline -n  # list the n last commits
```

## Rebasing helpers

My workflow with git is based on a clean history, with one and only one commit for each feature or fix of the application (following [conventional commits](https://www.conventionalcommits.org/){:target="_blank"} specification), and no merge commits. While this gives a very pleasant history, it also means using a lot of rebasing functionalities and a lot of force pushing.

To do so I have the forced version of the push presented before, `git fp`. I also have a command to amend everything in the current commit, `git amend`. The combination of those two is `git afp`, for **a**mend and **f**orce **p**ush, redoubtably efficient and dangerous, but I constantly use it!

```bash
# force push to the same branch name
fp = !git push origin $(git rev-parse --abbrev-ref HEAD) -f
# amend everything in the current commit
amend = !git add -A && git commit --amend --no-edit
# amend and force push afp = !git amend && git fp ```
```

For the rebasing part, the main aliases I use are `git rom` (for **r**ebase **o**n **m**aster), which rebases the current branch on origin/master (after fetching it), `git rh` (for **r**eset **h**ard), which can be seen as a force pull and resets the current branch with the remote one, and `git ri <n>`  (for **r**ebase **i**nteractive), which allows an interactive rebasing of the n last commits.

```bash
# rebase on origin/master
rom = !git fetch --all && git rebase origin/master
# reset the branch to the distant one
rh = !git fetch && git reset --hard origin/$(git rev-parse --abbrev-ref HEAD)
# interactive rebasing
ri = "!f() { git rebase -i HEAD~$1; }; f"
```

## <a name="summary"></a>But how do I set these aliases ?

You can set an alias with the config command of git, eg `git config --global alias.co checkout`, but the easiest is to write them directly in the `.gitconfig` file in your home directory, in the `[alias]` section.

Here is the list of all my aliases presented above (plus `git cp` which copies the current commit hash, handy for cherry-picking).

```bash
co = checkout
br = branch
ci = commit
st = status

nb = checkout -b  # create a new branch
del = branch -D  # delete a branch

# give a quick look at the state of the repo
state = !git fetch --prune && git fetch --tags && clear && git branch -vv && git status
ll = !git log --oneline -n  # list the n last commits

# stage and commit all the changes
c = !git add -A && git commit -m   
# stage and commit all the changes with WIP as a commit message
wip = !git add -A && git commit -m 'WIP'
drop = !git stash && git stash drop  # drop current changes

# push to the same branch name
p = !git push -u origin $(git rev-parse --abbrev-ref HEAD)
# force push to the same branch name
fp = !git push origin $(git rev-parse --abbrev-ref HEAD) -f
# amend everything in the current commit
amend = !git add -A && git commit --amend --no-edit
# amend and force push
afp = !git amend && git fp

# rebase on origin/master
rom = !git fetch --all && git rebase origin/master
# reset the branch to the distant one
rh = !git fetch && git reset --hard origin/$(git rev-parse --abbrev-ref HEAD)
# interactive rebasing
ri = "!f() { git rebase -i HEAD~$1; }; f"

# copy current hash in the clipboard 
cp = !git rev-parse HEAD | xargs echo -n  | xclip -selection clipboard 
```
