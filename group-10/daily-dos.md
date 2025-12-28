---
description: This page consists of daily hacks/learning
icon: forward-fast
---

# Daily Do's

## Indie Page

* Initial reserch on \[Indie page]\([https://indiepa.ge/marclou](https://indiepa.ge/marclou))

## How to open two git branches in single editor?&#x20;

* If you need to open two git branches in a single editor, then we need to utilise `worktree` in **git**
* Considering I am inside a folder named _folderA_ and it consists of branches named **branch-a** and **branch-b**
  * I can open the **branch-a** in another windows using worktree, via below commands

```
ubuntu@home: pwd
/usr/home/folderA
ubuntu@home: git branch -a
branch-a
branch-b
ubuntu@home: git branch
branch-a
ubuntu@home: git worktree add replica-of-folderA branch-b
```

### Command syntax

git worktree add <mark style="color:orange;">\<random-folder-name></mark> <mark style="color:blue;">\<remote-branch-to-checkout-in-local></mark>











