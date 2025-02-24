---
date: '2025-02-18T15:51:33+01:00'
draft: false
title: 'Five things you may need to do with Git'
---

Git is incredibly powerful, but many developers stick to the same basic commands without exploring its full potential. In this post, I’ll walk you through some simple yet powerful Git commands that you might not be using—but should. These are not advanced or obscure features, just practical enhancements to common tasks that can make your workflow smoother and more efficient.
## 1. Log commits in a readable manner
```
git log --oneline
```

The key here is `--oneline`. By default, git log prints at least five lines per commit, like this:

```
commit 33cf0c0a5bc661de98a76d2a1d382346e76fa930
Author: Jakob Busk Sørensen <jbsn@foss.dk>
Date:   Fri Feb 14 10:13:58 2025 +0100

    Added 'switching-to-hugo.md' (draft).
```

Adding the `--oneline` flag condenses each commit into a single line, making the log much more compact and readable:

```
478df7b (HEAD -> main, origin/main) Added a navigation bar.
33cf0c0 Added 'switching-to-hugo.md' (draft).
57e7816 Changed repository name to blog (lower case b)
(...)
```

## 2. Stage all files
```
git add -A
```

This is an alternative to `git add .`, which I often see used, and is equivalent to `git add --all`. The key difference is that `git add .` only includes the current directory and its subdirectories. So if you run this command outside the root directory, you might not stage all changes. 

If in doubt use `git add -A`.

## 3. Undo last commit
```
git reset HEAD~1
```

This command is useful when you accidentally committed something you shouldn't have. It will take you back one commit, effectively acting as an _undo_ for the most recent commit. An alternative is `git revert <sha>` (where `<sha>` is the commit hash). Unlike `git reset`, `git revert` creates a new commit that undoes the changes, preserving history.

⚠️ **If you ever commit a secret and push it to a remote repository, reverting is not enough — change the secret immediately.**

## 4. Include additional changes in last commit
```
git commit --amend --no-edit
```

Did you forget to stage a file before committing? Or maybe you missed a semicolon, breaking all the unit tests? With `git commit --amend --no-edit`, you can add those changes to your latest commit while keeping the same commit message.

However, only use this command for changes that were always meant to be part of the commit — not to create bloated, ever-growing commits.

## 5. Remove untracked files
```
git clean -f
```

If you added new files that you don't want to commit, you can delete them with this command. You can use `git clean -fd` to also remove directories.

This only works on unstaged files and directories.