---
title: Github Managing Forks
author: Christian Külker
date: 2022-06-25
version: 0.1.1
type: doc
disclaimer: True
toc: True
categories:
- Git
tags:
- Github-Managing-Forks
commands:
- git
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

**Set up managing fork via upstream**  (drawback, local repo will be ahead)

```bash
git remote show
git remote add upstream https://github.com/whoever/whatever.git
git remote show
git remote show upstream
```

**Update fork via 'upstream'** (Update only, no local commits expected)

```bash
git remote show upstream # make sure result is 'fast-forwardable'
git fetch upstream
git branch -va # make sure if the branches not changed (e.g. main->dev)
export B=$(git branch -va|grep 'origin/HEAD'|sed -e 's%.*remotes/origin/HEAD.*->\s\+origin/%%g')
git checkout $B # See if $B is main/develop/...: see remotes/origin/HEAD -> origin/BRANCH
git merge upstream/$B # See if $B is main/develop/...
git push
```

If done via GUI (_"Fetch upstream"_), it will also add a merge commit like
_"Merge branch 'corona-warn-app:release/2.24.x' into development"_ (if the HEAD
points to development) and leave the repository +1 commits ahead.

## Adding A Remote Upstream

To understand the current situation of a git repository - any repository,
regardless if it is a fork or not - the git remote command can be used.

```bash
git remote show
origin
```

Usually it just shows the word `origin` in case the repository is a fork. When
specifying the name `origin` more information can be displayed.

```bash
git remote show origin
```

For the repository `cwa-documentation` it will give for example:

```
* remote origin
  Fetch URL: git@github.com:ckuelker/cwa-documentation.git
  Push  URL: git@github.com:ckuelker/cwa-documentation.git
  HEAD branch: main
  Remote branches:
    SabineLoss-patch-1                                   tracked
    SabineLoss-patch-2                                   tracked
    fix/typo-overview-security.md-identification-against tracked
    main                                                 tracked
  Local branch configured for 'git pull':
    main merges with remote main
  Local refs configured for 'git push':
    fix/typo-overview-security.md-identification-against pushes to \
fix/typo-overview-security.md-identification-against (up to date)
    main                                                 pushes to \
main                                                 (up to date)
```

After the configuration details of pushing and fetching and the current status
where the head of the repository is pointing at (main), information about
remote and local branches are displayed.

```bash
git remote add upstream https://github.com/whoever/whatever.git
```

Example:

```bash
git remote add upstream https://github.com/corona-warn-app/cwa-documentation.git
```

To confirm that a remote entity was added successfully the `git remote` command
is useful.

```bash
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

```bash
git remote show upstream
* remote upstream
  Fetch URL: https://github.com/corona-warn-app/cwa-documentation.git
  Push  URL: https://github.com/corona-warn-app/cwa-documentation.git
  HEAD branch: main
  Remote branches:
    SabineLoss-patch-1                           tracked
    SabineLoss-patch-2                           tracked
    fix/typo-backend-infrastructure-architecture tracked
    main                                         tracked
    tkowark-patch-1                              tracked
    tune_linting                                 tracked
  Local ref configured for 'git push':
    main pushes to main (fast-forwardable)
```

The above shows an up to date situation. The following show an out of sync
situation (output truncated):

```bash
  ...
  Local branch configured for 'git pull':
    main merges with remote main
  Local ref configured for 'git push':
    main pushes to main (local out of date)
```

## Updating the fork

There are more than one approach to update a fork. In trivial cases the GUI of
the git repository provides (for example `github.com`) a method to do so. As
this can change with a new release of the GUI a more generic method is to add a
remote upstream, fetch the upstream, checkout main and merge upstream and main.

Keeping the fork up-to-date is not absolutely necessary. If working on
anything more than just a tiny quick fix, make sure you keep your fork up to
date by tracking the original "upstream" repo that you forked. To do this,
you'll need to add a remote:

```bash
$ git remote add upstream https://github.com/UPSTREAM-USER/ORIGINAL-PROJECT.git
```

Verify the new remote named 'upstream' with `git remote -v`. See this example
for `cwa-documentation.git`.

```bash
git remote -v
origin  git@github.com:ckuelker/cwa-documentation.git (fetch)
origin  git@github.com:ckuelker/cwa-documentation.git (push)
upstream        https://github.com/corona-warn-app/cwa-documentation.git (fetch)
upstream        https://github.com/corona-warn-app/cwa-documentation.git (push)
```

Update the repository with the latest changes from upstream: fetch latest
commits and branches.

```bash
$ git fetch upstream
````

View all branches, including those from upstream

```bash
$ git branch -va
```

Or if you would like to automate the search.

```bash
export B=$(git branch -va|grep 'origin/HEAD'|sed -e 's%.*remotes/origin/HEAD.*->\s\+origin/%%g')
```

Checkout the main branch and merge the upstream repository main branch.
Sometimes the main branch is called 'main', sometimes other names like
'development'

```bash
$ git checkout $B
$ git merge upstream/$B
```

In case there are no unique commits on the local main branch, git will simply
perform a fast-forward. However, if changes have been made to the source
(upstream) repository (this probably should not be done) conflicts may occur.
Be careful to changes made by upstream. If changes had been done to the
upstream, you should consider 2 ways. One is to just follow upstream. If this
is the case only update the fork if your pull request was successfully
integrated into the upstream repository. Or second is to develop new software
on the basis of the fork. Then synchronizing the fork should not be done in a
very controlled way. If you find yourself in the second category and are
forced to make many, large and complex synchronizations to your fork, you
should consider to abandon your fork and contribute to the upstream repository.

```bash
$ git push
```

## Additional Steps

You are done, if you just forked the other repository. The next steps depends
on the question (1) what you have done to the fork: history and (2) what is
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

- [How do I update a `github` forked repository]
- [Syncing a fork]

[How do I update a github forked repository]: https://stackoverflow.com/questions/7244321/how-do-i-update-a-github-forked-repository/7244456#7244456
[Syncing a fork]: https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/syncing-a-fork

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2022-06-25 | Examples, update TLTR, shell->bash, commands, main   |
| 0.1.0   | 2020-09-24 | Initial release                                      |

