---
title: Github Pull Request
linkTitle: Github-Pull-Request
author: Christian Külker
date: 2020-06-08
version: 0.1.0
type: doc
disclaimer: True
TOC: True
categories:
- Git
commands:
- git
tags:
- Github pull request
- PR
- branch
- fork
description: How to make a github.com pull request
...

Participation in git repositories often is done via a pull request, because not
all people have write access to the main repository. A pull request solves this
issue. This section is about how to make a pull request on `github.com`.

1. Log into `github.com`
2. Go to the web page projects repository, press fork button
3. Clone **your** fork of the repository

```shell
user@host:~$ git clone git@github.com:USER/REPOSITORY.git
```

4. Enter the REPOSITORY

```shell
user@host:~$ cd REPOSITORY
```

5. Switch to the master branch (your branch should be diverted from master) and
look for the other branches, so that your new name do not conflict. For
example.

```shell
user@host:~$ git checkout master
user@host:~$ git branch -a
* master
remotes/origin/HEAD -> origin/master
remotes/origin/SabineLoss-patch-1
remotes/origin/SabineLoss-patch-2
remotes/origin/master
```

6. Crate a new self descriptive branch with a `BRANCHNAME`

```shell
git branch BRANCHNAME
```

7. Switch to the new branch

```shell
git checkout BRACHNAME
```

The short form of the last 2 steps, create a self descriptive branch of
intended change.

```shell
user@host:~$ git checkout -b BRANCHNAME
```

For example

```shell
user@host:~$ git checkout -b fix/typo-overview-security.md-identification-against
```

Make sure you are at your branch (example from above)

```shell
user@host:~$ git branch -a
* fix/typo-overview-security.md-identification-against
  master
  remotes/origin/HEAD -> origin/master
  remotes/origin/SabineLoss-patch-1
  remotes/origin/SabineLoss-patch-2
  remotes/origin/master
```

8. Create very few commits with **good** commit messages

```shell
user@host:~$ sed -i -e 's%A%B%' file.md
user@host:~$ git commit -m 'typo (A -> B)' file.md
```

9. Cleanup the branch before pushing

10. Pushing the branch to `github.com`

When using just `git push` you will get an error message like this

```shell
user@host:~$ git push
fatal: The current branch BRANCHNAME has no upstream branch. To push the
current branch and set the remote as upstream, use

    git push --set-upstream origin BRANCHNAME
```

However you can just push it with a one time statement

```shell
user@host:~$ git push origin BRANCHNAME
```

Example

```shell
user@host:~$ git push origin fix/typo-overview-security.md-identification-against
Counting objects: 6, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (6/6), done.
Writing objects: 100% (6/6), 604 bytes | 0 bytes/s, done.
Total 6 (delta 4), reused 0 (delta 0)
remote: Resolving deltas: 100% (4/4), completed with 2 local objects.
remote:
remote: Create a pull request for
        'fix/typo-overview-security.md-identification-against' on GitHub by
        visiting:
remote:      https://github.com/ckuelker/cwa-documentation/pull/new/\
             fix/typo-overview-security.md-identification-against
remote:
To github.com:ckuelker/cwa-documentation.git
 * [new branch]      fix/typo-overview-security.md-identification-against -> \
                     fix/typo-overview-security.md-identification-against
```

11. Request A Pull

The last commit already shows the URL to go
`https://github.com/UPSTREAM-USER/UPSTREAM-REPO/pull/new/BRANCHNAME`. Use the
URL and press the green button "Compare & pull request". Add a message.

## Understanding The Remote Origin

```shell
user@host:~$ git remote show origin
```

Example

```shell
user@host:~$ git remote show origin
* remote origin
  Fetch URL: git@github.com:ckuelker/cwa-testresult-server
  Push  URL: git@github.com:ckuelker/cwa-testresult-server
  HEAD branch: master
  Remote branches:
    fix/no-nullable-result                          tracked
    fix/typo-docs-architecture-overview.md-security tracked
    master                                          tracked
  Local branch configured for 'git pull':
    master merges with remote master
  Local refs configured for 'git push':
    fix/typo-docs-architecture-overview.md-security pushes to \
      fix/typo-docs-architecture-overview.md-security (up to date)
    master                                          pushes to master \
                                           (up to date)
```

## Delete An Unused Branch

Sometimes while working on the pull request one realize that the name of the
branch is not fit for the purpose, needs an addition. Usually the `-d` option
is used.

```shell
user@host:~$ git branch -d BRANCHNAME
```

However if the branch is not merged `-D` needs to be used.

```shell
user@host:~$ git branch -D BRANCHNAME
```

## Keeping Up-To-Date

See [managing forks](github-manageing-forks.html) for details. 


## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2020-06-08 | Initial release                                      |
