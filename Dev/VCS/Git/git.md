---
title: Git
author: Christian Külker
date: 2022-04-13
version: 0.1.0
type: doc
disclaimer: True
TOC: True
categories:
- VCS
- Git
commands:
- git
tags:
- Git
- VCS
- Reconcile Divergent Branches
- foxtrot merges
description: Collection of git recipes


## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2022-04-13 | Initial release                                      |


# Reconcile Divergent Branches

From git 2.27.0 onwards the user is confronted with a similar message from git,
when using `git pull`.

~~~
Warning: Pulling without specifying how to reconcile divergent branches is
discouraged. You can squelch this message by running one of the following
commands sometime before your next pull:

  git config pull.rebase false  # merge (the default strategy)
  git config pull.rebase true   # rebase
  git config pull.ff only       # fast-forward only
~~~

Newer version do basically the same:

~~~
hint: Pulling without specifying how to reconcile divergent branches is
hint: discouraged. You can squelch this message by running one of the following
hint: commands sometime before your next pull:
hint:
hint:   git config pull.rebase false  # merge (the default strategy)
hint:   git config pull.rebase true   # rebase
hint:   git config pull.ff only       # fast-forward only
hint:
hint: You can replace "git config" with "git config --global" to set a default
hint: preference for all repositories. You can also pass --rebase, --no-rebase,
hint: or --ff-only on the command line to override the configured default per
hint: invocation.
~~~

Which solution should be chosen?

I am not an expert on this, but here are my thoughts. This question is about
which strategy to be used in case of a pull that would create a minor local
"disturbance": 1) merge 2) re-base or 3) fast-forward only.

It seems that the previous default (merge) strategy was not sane and the git
developers decided to ask the user to chose a sane (or at least known) strategy
(to the user). I think it is a good practice to ask and not to change the
default behavior without asking and therefore risk to break work flow or code
of users. The previous default strategy (merge) had the risk of the so called
[foxtrot merges](https://blog.developer.atlassian.com/stop-foxtrots-now/) where
the order of the first HEAD and the second entry gets messed up. So `git config
pull.rebase false` seems a risky option.

The `git pull --help` page looks innocent at the beginning. However the default
is that this is a short form of `git fetch&&git merge FETCH_HEAD`. That can
result in a merge commit (with or without foxtrot does not matter so much).
This means that pulling from a remote repository is not a harmless operation as
this is not a pure download and it might change the commit history by adding
(not committed) stuff from your hard disk. And it might even include your own
work into git, even though you did not anticipated it or you deliberately
wanted to commit it later or not at all. And you might not even notice it.
Let's be honest.  Who is reading all the gibberish git is writing all the time?

So if the first (default) option is evil, one should take the second one and
make a `git fetch&&git rebase` for every `git pull`? Well, this circumvent
foxtrot merges as the commit history is linear (clean). The local master would
be on top of the remote origin/master. But still it changes the commit history
(sometimes) without asking. Other (old) side effects, like the [git history is
a bunch of
lies](https://stevebennett.me/2012/02/24/10-things-i-hate-about-git/) not
considered.

In my opinion the `git pull --ff-only` is the best solution. This will complain
if the operation would need a merge, or a re-base or whatever and not a clean
fast-forward download. So executing `git config pull.ff only` will get rid of
the warning. If you are even more convinced of this solution you can make it
global: `git config --global pull.ff only`

While researching, almost at the end of writing, I stumbled over the
[sffc's Tech Blog](https://blog.sffc.xyz/post/185195398930/why-you-should-use-git-pull-ff-only-git-is-a)
with nice graphics that has a simliar view on the topic but explain it better.

To summarize (and a suggestion to improve up on the message [attention this is
humor!]):

~~~
- `git config pull.rebase false` # occasionally making sneaky commits of own
                                   work with a dirty history that sometimes
                                   dance foxtrot (and may break the repository)
- `git config pull.rebase true`  # occasionally making sneaky commits of own
                                   work with a clean history (of lies)
- `git config pull.ff only`      # stop downloading if local work would be
                                   overwritten or committed and do not change
                                   (or lie about) history

~~~

