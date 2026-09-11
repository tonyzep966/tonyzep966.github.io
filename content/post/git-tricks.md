---
title: 'Git Tricks Cheat Sheet'
date: 2026-09-11T11:22:24+08:00
lastmod: 2026-09-11T11:22:24+08:00
cover: "images/banner.webp"
summary: "A handy cheat sheet for common Git commands and tricks to deal with various Git scenarios."
description: "A handy cheat sheet for common Git commands and tricks to deal with various Git scenarios."
---

## Remove the latest commit from the remote branch while keeping it locally

```bash
git fetch origin
git switch <branch-name>
git reset --soft HEAD~1
git push origin <branch-name> --force-with-lease
```

## Modify a historical commit

```bash
# Omit fetching and switching branches if already on the correct branch
# Assuming that we already have some changes on working directory
git add <file>
git commit --fixup HEAD~1 # Target the commit one step back from HEAD
git rebase -i --autosquash HEAD~2
git push --force-with-lease
```

`--autosquash` automatically moves the fixup commit to the correct position during an interactive rebase.

## Branch Renaming

### Scenario 1: Someone has renamed the remote branch

For example, the branch `old-branch-name` has been renamed to `new-branch-name` on the remote.

```bash
# Rename the local branch
git branch -m old-branch-name new-branch-name
git fetch origin
# Set the upstream for the renamed branch
git branch -u origin/new-branch-name new-branch-name
# Optionally, prune the deleted remote branch reference in the local repository
git remote prune origin

# If operating the default branch, update the remote's HEAD to point to the new default branch
# this is often useful when maintainer have renamed the default branch on the remote.
git remote set-head origin -a
```

### Scenario 2: Rename the local branch then push it to the remote

```bash
git branch -m old-branch-name new-branch-name
git push origin new-branch-name
git push origin --delete old-branch-name
```

## A Way to Archive a Branch using Tag

```bash
git tag archive/<branch-name> <branch-name>
# Flag -D deletes the branch locally
git branch -D <branch-name>
# Push the tags to the remote repository
git push --tags
# Delete the branch from the remote repository
git push origin :<branch-name>
```

To restore the archived branch, create a new branch from the tag:

```bash
git checkout -b <branch-name> archive/<branch-name>
```