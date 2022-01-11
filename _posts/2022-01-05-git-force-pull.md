---
layout: post
title: 'Git: how can I "force" pull a distant repository'
date: 2022-01-05
last_modified_at: 2022-01-05
cover_image: 2022-01-05-feature.jpg
author: Simon Dosda
categories: git tool
excerpt_separator: <!--more-->
---

> Photo by [Vidar Nordli-Mathisen](https://unsplash.com/@vidarnm).

Sometimes you want to set the state of your local branch to its remote version but are not able to do so using the `git pull` command, because your local version and the remote one have diverged.

<!--more-->

This can happen for several reasons, for me the two main ones are:

- I have checked out on a colleague branch for a review, he has made some changes on it that have changed the branch history and I want to pull this new version
- I have messed up my history during a rebase with conflicts and want to start again from the remote version

In these cases, using `git pull` will try to merge or rebase the remote branch into your local one.

But what you want instead is to start over with the distant branch. This could naively be done by deleting your local branch before pulling the distant one, but that is a bit cumbersome.

Thankfully, git provides us with a more handy way to manage this scenario with the `reset --hard` command.

```bash
 git reset --hard origin/my-branch
```

`git reset` is a command that took me some time to understand but worth knowing. If you are interested I have [written an article about its possibilities]({post 2022-01-04-git-reset}).

Basically, the `reset` command set our head to the targeted commit. Using it with the `--hard` option allows to discard all local changes, committed or not, and therefore force the current state to the distant one.

If you find yourself using it often, I would advise you [to set an alias for it](https://simondosda.github.io/posts/2021-04-13-git-aliases.html).

When modifying your history, you might end up losing commit that you wanted to keep. Fortunately, [there is a way to retrieve these commits]({post 2022-01-06-git-lost-commits }) that are no longer in your branch.
