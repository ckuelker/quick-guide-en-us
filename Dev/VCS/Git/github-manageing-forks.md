---
title: Github Managing Forks
author: Christian Külker
date: 2020-09-24
version: 0.1.0
type: doc
disclaimer: True
TOC: True
categories:
- Git
tags:
- Github-Managing-Forks
description: How to manage github.com forks

---

Managing forks can be done in different ways. One way is to manage forks via
the web interface. While this has the advantage of no additional configuration
in the forked repository, it has the disadvantage of a complex workflow and can
not be applied to all cases. In addition it requires to open several views
(open browser tabs) of different repositories which in some cases can lead to
open a pull request at the wrong repository. And of course those requests can
not be deleted and mark the error for eternity. Because of this an other method
that uses the command line and the additional configuration of an upstream
remote origin will be described in this document.

## To Long To Read - TLTR

```shell
git remote show
git remote add upstream https://github.com/whoever/whatever.git
git remote show
git remote show upstream
```

## Adding A Remote Upstream

To understand the current situation of a git repository - any repository,
regardless if it is a fork or not - the git remote command can be used.

```shell
git remote show
origin
```

Usually it just shows the word `origin` in case the repository is a fork. When
specifying the name `origin` more information can be displayed.

```shell
git remote show origin
```

For the repository `cwa-documentation` it will give for example:

```
* remote origin
  Fetch URL: git@github.com:ckuelker/cwa-documentation.git
  Push  URL: git@github.com:ckuelker/cwa-documentation.git
  HEAD branch: master
  Remote branches:
    SabineLoss-patch-1                                   tracked
    SabineLoss-patch-2                                   tracked
    fix/typo-overview-security.md-identification-against tracked
    master                                               tracked
  Local branch configured for 'git pull':
    master merges with remote master
  Local refs configured for 'git push':
    fix/typo-overview-security.md-identification-against pushes to \
fix/typo-overview-security.md-identification-against (up to date)
    master                                               pushes to \
master                                               (up to date)
```

After the configuration details of pushing and fetching and the current status
where the head of the repository is pointing at (master), information about
remote and local branches are displayed.

```shell
git remote add upstream https://github.com/whoever/whatever.git
```

```shell
git remote add upstream https://github.com/corona-warn-app/cwa-documentation.git
```

To confirm that a remote entity was added successfully the `git remote` command
is useful.

```shell
git remote show
origin
upstream
```

It should show the word `upstream`.

Similar the information of the `remote origin` also for the `remote upstream`
detailed information can be displayed via the `git remote show upstream`
command.

For the `cwa-documentation` repository the upstream information looks as
follows:

```shell
git remote show upstream
* remote upstream
  Fetch URL: https://github.com/corona-warn-app/cwa-documentation.git
  Push  URL: https://github.com/corona-warn-app/cwa-documentation.git
  HEAD branch: master
  Remote branches:
    SabineLoss-patch-1                           tracked
    SabineLoss-patch-2                           tracked
    fix/typo-backend-infrastructure-architecture tracked
    master                                       tracked
    tkowark-patch-1                              tracked
    tune_linting                                 tracked
  Local ref configured for 'git push':
    master pushes to master (fast-forwardable)
```

## Updating the fork

There a more than one approach to update a fork. In trivial cases the GUI of
the git repository provide (for example `github.com`) provide a method to do
so. As this can change with a new release of the GUI a more generic method is
to add a remote upstream, fetch the upstream, checkout master and merge
upstream and master.

Keeping the fork up-to-date is not an absolutely necessary step. If working on
anything more than just a tiny quick fix, make sure you keep your fork up to
date by tracking the original "upstream" repo that you forked. To do this,
you'll need to add a remote:

```shell
$ git remote add upstream https://github.com/UPSTREAM-USER/ORIGINAL-PROJECT.git
```

Verify the new remote named 'upstream'

```shell
git remote -v
```

Update the repository with the latest changes from upstream: fetch latests
commits and branches.

```shell
$ git fetch upstream
````

View all branches, including those from upstream

```shell
$ git branch -va
```

Checkout the master branch and merge the upstream repository master branch:

```shell
$ git checkout master
$ git merge upstream/master
```

In case there are no unique commits on the local master branch, git will simply
perform a fast-forward. However, if changes have been making to master (this
probably should not be done) conflicts may occur. Be careful to changes made by
upstream.

```shell
$ git push
```

## Additional Steps

You are done, if you just forked the other repository. The next steps depends
on the question (1) what you have done to the fork: history  and  (2) what is
the intended use: usage.

The history question (1) can be divided into:

1. Nothing (no changes, just a fork)
2. Changes (changes in the work tree: nothing added or committed)
3. Add changes
4. Add changes and committed
5. Add changes and committed and created a pull request
6. Add changes and committed and created a pull request that was accepted

The use question (2) can be divided into:

1. Users should be able to use a fork with a pristine commit history
2. Users should be able to use a fork with a different commit history

## Further Reading

- [How do I update a github forked repository]

[How do I update a github forked repository]: https://stackoverflow.com/questions/7244321/how-do-i-update-a-github-forked-repository/7244456#7244456

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2020-09-24 | Initial release                                      |

