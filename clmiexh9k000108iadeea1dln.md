---
title: "How To Fix Git Refs With Case Sensitivity"
datePublished: Thu Sep 14 2023 00:08:45 GMT+0000 (Coordinated Universal Time)
cuid: clmiexh9k000108iadeea1dln
slug: how-to-fix-git-refs-with-case-sensitivity
tags: git

---

## Symptoms I Observed On a Mac

Every time I performed a `git fetch` or a `git pull` on my MacBook for a particular repository I would see a log from the command that said something like the following.

```bash
From github.com:
 * [new branch]      User/feature -> origin/User/feature
```

What I noticed though, that branch was an old and outdated one that nobody was working on.  I came to find out it hadn't been updated for 11 months!  I had been seeing this message from github.com for long enough...  I had to track it down to remove it.

## The Fix

Git provides a way to _migrate the refs_ on your local machine to a flat file with case sensitivity, instead of the default directory structure which may be case-insensitive.

Run this command from your repository root:

```bash
git pack-refs --all
```

https://git-scm.com/docs/git-pack-refs

Then do a `git pull` or `git fetch`, and you'll stop seeing the "new branch" because the ref will use the flat file with the correct case.

## The Root Problem & Explanation

Git refs may be stored case-sensitive in one filesystem, but case-insensitive in another.

This can cause some references in git to be incorrect according to your local filesystem.

In my scenario that I observed (every day) the branch was named on the remote using capital letters, `User/feature`.  To add to the mix there were other branches named lowercase, `user/feature2`.  So when refs were being stored in my filesystem it would put the branch under whatever one existed first, which happened to be the lowercase one.

```bash
user
  - feature
  - feature2
```

Ideally it would store them like this so git can correctly find the ref.

```bash
User
  - feature
user
  - feature2
```

When the refs are stored in a flat file the case sensitivity can be preserved, so you won't see the new branch warning anymore.

## Disclaimer

Over time when branches are updated, there's a chance you will have to run the command again, this only affects your local machine's refs.

An alternative approach could be to rename the offending branch on the remote itself.