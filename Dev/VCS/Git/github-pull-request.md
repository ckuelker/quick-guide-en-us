---
title: Github Pull Request
linkTitle: Github-Pull-Request
author: Christian Külker
date: 2024-02-27
version: 0.1.4
type: doc
disclaimer: True
toc: True
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

---

## Introduction

A common way to participate in git-managed software projects is to submit a
"pull request" to certain services such as <https://github.com> (it's called a
"merge request" on <https://gitlab.com>). This technique is very popular with
git because people often have a one-time commitment and do not have write
access to the repository. A pull request is therefore a one-time __write__
exception to the repository. In traditional `VCS`, for example, a tarball would
be emailed, and the repository maintainer would manually save the tarball to
the file-system, extract the contents and add the changes to the repository,
review it, make a diff, and decide whether to include the changes in the next
commit of the repository to allow for a one-time contribution. This was a
labor-intensive, error-prone process. Git and _GitHub_ use the concept of a
"pull request" and a "fork" to provide a semi-automated method for a one-time
write contribution. This section is about how to make a pull request (and fork)
on _GitHub_ specifically. As this is not a Git feature as such, the web
interface of _GitHub_ must be used in conjunction with the command line.

1. Log into <https://github.com>
2. Go to the website projects repository, press the fork button
3. Clone __your__ fork of the repository

```bash
user@host:~$ git clone git@github.com:USER/REPOSITORY.git
```

4. Enter the REPOSITORY

```bash
user@host:~$ cd REPOSITORY
```

5. Switch to the main or master branch (your branch should be redirected from
   the master) and check the other branches so that your new name does not
   conflict. For example

```bash
user@host:~$ git checkout master
user@host:~$ git branch -a
* master
remotes/origin/HEAD -> origin/master
remotes/origin/SabineLoss-patch-1
remotes/origin/SabineLoss-patch-2
remotes/origin/master
```

6. Crate a new self descriptive branch with a `BRANCHNAME`

```bash
git branch BRANCHNAME
```

7. Switch to the new branch

```bash
git checkout BRACHNAME
```

The short form of the last 2 steps, create a self-describing branch of the
intended change.

```bash
user@host:~$ git checkout -b BRANCHNAME
```

For example

```bash
user@host:~$ git checkout -b fix/typo-overview-security.md-identification-against
```

Make sure you are at your branch (example from above)

```bash
user@host:~$ git branch -a
* fix/typo-overview-security.md-identification-against
  master
  remotes/origin/HEAD -> origin/master
  remotes/origin/SabineLoss-patch-1
  remotes/origin/SabineLoss-patch-2
  remotes/origin/master
```

8. Create very few commits with __good__ commit messages

```bash
user@host:~$ sed -i -e 's%A%B%' file.md
user@host:~$ git commit -m 'Missspelling (A -> B)' file.md
```

9. Cleanup the branch before pushing

10. Pushing the branch to `github.com`

When using just `git push` you will get an error message like this

```bash
user@host:~$ git push
fatal: The current branch BRANCHNAME has no upstream branch. To push the
current branch and set the remote as upstream, use

git push --set-upstream origin BRANCHNAME
```

However you can just push it with a one time statement

```bash
user@host:~$ git push origin BRANCHNAME
```

Example

```bash
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

After the push the GitHub Web Interface shows a message:

> BRANCHMAME had recent pushes X seconds|minutes ago [Compare & pull request]


11. Request A Pull

The last commit already shows the URL to go to
`https://github.com/UPSTREAM-USER/UPSTREAM-REPO/pull/new/BRANCHNAME`. Use the
URL and press the green "Compare & Pull Request" button. Add a message.

_OR_ use the button unte GitHub Web interface mentioned at the end of the
last section.

## Understanding The Remote Origin

```bash
user@host:~$ git remote show origin
```

Example

```bash
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

Sometimes, while working on a pull request, one realizes that the name of the
branch is not appropriate, that an addition is needed. Usually the `-d` option
is used.

```bash
user@host:~$ git branch -d BRANCHNAME
```

However, if the branch is not merged, `-D` must be used.

```bash
user@host:~$ git branch -D BRANCHNAME
```

## Keeping Up-To-Date

See [managing forks](github-manageing-forks.html) for details.

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.4   | 2024-02-27 | Small improvements and changes in formatting         |
| 0.1.3   | 2023-05-08 | Improve writing                                      |
| 0.1.2   | 2022-06-23 | Improve introduction                                 |
| 0.1.1   | 2022-05-31 | Fix front matter, shell->bash                        |
| 0.1.0   | 2020-06-08 | Initial release                                      |
