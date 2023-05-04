---
title: Git With Other Default Branch
linkTitle: Git-New-Default-Branch
author: Christian Külker
date: 2023-05-04
version: 0.1.2
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

## Default Git Branch

There is no way I know of to query the default branch name that git will use
for a new repository. As of 2023-05-04 it is `master`.

## Manually Changing Default Branch At Creation Time

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

## Setting Globally a Different Branch Per Default

The previous section described the situation where you are not happy with the
default branch name. As of Git 2.28.0, release July 2020, it is possible to use
the `init.defaultBranch` configuration option, which allows Git users to define
and configure a default branch name other than `master`.

For example, Gitlab changed the default branch from `master' to `main' in 2021.

### Example

To change the default branch name in Git from `master` to `bonanza` (for
example) for all new repositories, you can use the `init.defaultBranch`
configuration setting. This setting was introduced in Git 2.28.0, so make sure
you have a compatible version installed.

You can check your Git version using the following command:

```bash
git --version
```

If you have a compatible version, you can use this command to set the default
branch name:

```bash
git config --global init.defaultBranch bonanza
```

This will set the default branch name to `bonanza` for all new repositories
created by the current user. If you want to set it for a specific repository
only, you should navigate to that repository and run this command without the
`--global` flag:

```bash
git config init.defaultBranch bonanza
```

## Changing The Branch in Exsisting Repositories

Note that the above receipt will not change the default branch name in existing
repositories. For existing repositories you will need to manually rename the
branch. Here is how to do it:

1. Switch to the branch you want to rename:

    ```bash
    git checkout master
    ```

2. Rename the branch:

    ```bash
    git branch -m bonanza
    ```

3. Push the renamed branch and reset the upstream branch:

    ```bash
    git push origin -u bonanza
    ```

4. Finally, you may want to delete the old branch in the remote repository:

    ```bash
    git push origin --delete master
    ```

Remember to inform your staff/community/co-workers about this change, as it
will affect their clones.

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2023-05-04 | Improve writing, Git 2.80.0, init.defaultBranch      |
| 0.1.1   | 2023-05-03 | Improve writing                                      |
| 0.1.0   | 2022-07-15 | Initial release                                      |
