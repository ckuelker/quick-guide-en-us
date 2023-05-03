---
title: Git With Other Default Branch
linkTitle: Git-New-Default-Branch
author: Christian Külker
date: 2023-05-03
version: 0.1.1
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Git
- VCS
commands:
- git
- touch
- cat
tags:
- git
- git init
- git checkout
- git add
- git commit
- HEAD
- refs
description: How to create a new Git repository with a non-default branch

---

Sometimes the default branch name is not what you want. However, this branch
name is hard-coded into `git`. One way to get around using the hard-coded
default branch is to create a virtual new branch __before__ the first commit.

When a new `git` repository is created, the `HEAD` file is at that moment a
pointer to the (virtual) default branch. You can easily find out what it points
to with `cat`. The trick is to change the pointer for `HEAD` __before__ the
first commit, and therefore before the default branch exists, and make the new
virtual first branch the default branch.

For all steps, we will assume that a new repository has been created, similar
to this approach.

```bash
cd /tmp
mkdir test
cd test
git init
```

(`VS;PR`) Now the __very short__ process of creating a git repository with a
different default branch, without explanations, so __please read__ it.

```bash
cd /tmp
mkdir test
cd test
git init
git checkout --orphan main
```

(`ATL;DR`) The next example is basically (almost) the same, but includes some
commands to prove it and explanations of what to expect.

```bash
cd /tmp
mkdir test
cd test
git init                   # HEAD points to non existing ref: refs/heads/master
cat .git/HEAD              # Should give ref: refs/heads/master
ls .git/refs/heads         # Should give nothing
git checkout --orphan main # HEAD points to non existing ref: refs/heads/main
cat .git/HEAD              # Should give ref: refs/heads/main
ls .git/refs/heads         # Should give nothing (will change after commit)
```

(`TL;DR`) Finally, the following session shows what happens when there is a
first commit to the repository, including the output of all commands.

```bash
cd /tmp
mkdir test
cd test

git init                   # HEAD points to non existing ref: refs/heads/master
Initialized empty Git repository in /tmp/test/.git/

cat .git/HEAD              # Should give ref: refs/heads/master
ref: refs/heads/master

ls .git/refs/heads         # Should give nothing

git checkout --orphan main # HEAD points to non existing ref: refs/heads/main
Switched to a new branch 'main'

cat .git/HEAD              # Should give ref: refs/heads/main
ref: refs/heads/main

ls .git/refs/heads         # Should give nothing (will change after commit)

touch README.md            # Will create the file README.md

git add README.md          # Make the file known to git

                           # Create a commit and in unison the new branch
                           # under a non default name
git commit -m init README.md
[main (root-commit) 9fdf194] init
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 README.md

cat .git/HEAD              # Should give ref: refs/heads/main
ref: refs/heads/main

ls .git/refs/heads         # Should give main
main
```

As you can see, `ls .git/refs/heads` now shows that the new branch has been
created.

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2023-05-03 | Improve writing                                      |
| 0.1.0   | 2022-07-15 | Initial release                                      |

