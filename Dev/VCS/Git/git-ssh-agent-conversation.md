---
title: Git Conversations With The SSH Agent Conversation
linkTitle: Git-SSH-Agent-Conversation
author: Christian Külker
date: 2022-06-22
version: 0.1.0
locale: en_US
lang: en
type: doc
disclaimer: True
TOC: True
categories:
- Git
commands:
- ssh
- ssh-agent
- ssh-add
- git
- ps
- grep
- eval
tags:
- SSH
- Git
- Agent
- Eliza
- ed25519
- key
- public key
- private key
description: Different git conversations with the ssh agent.

---

When using the `ssh` command, `ssh` uses one key stored in `~/.ssh` and one
seldom need to deal with the details. However recently in the context of
switching identities and authorization away from passwords more often than not
`ssh` __public__ keys are used to control access. This is also the case with `git`.
While on most systems this his handled transparently, sometimes the wrong key
is used and access is denied even though it should not. This document deals
with the `ssh` key management, __agents__ and `git`.

For the sake of education, lets assume we generated a private and public key to
be used solely for the access to a commercial site under `~/.ssh` called
`id_ed25519_github`. So the following files exist:

- `~/.ssh/id_ed25519_github` (private key - we never touch this!)
- `~/.ssh/id_ed25519_github.pub` (public key)

As we are in a dangerous world to help with this kind of things we need an
__agent__.  Nicely `SSH` provide such an __agent__ called `ssh-agent`.

Usually this __agent__ is already started on your favorite OS: Debian. It is
very common to start the __agent__ together with the `X` session aka desktop.
However for some reasons unknown to me he usually is not very talkative (or
dormant if `X` is not started) at least when using the system remotely.

To understand if our __agent__ is alive and willing to communicate with
a mortal, one can see if the __agent__ is enlisted in the process list:

```bash
ps ax|grep ssh-agent|grep -v grep
  33178 ?        Ss     0:02 /usr/bin/ssh-agent x-session-manager
```

After we understood that we have an __agent__ alive, we might ask him about our
keys, oddly we do not execute `ssh-agent ask` or something but `ssh-add`, even
though we are not adding anything. Who would have thought?

```bash
ssh-add -l
```

This will list fingerprints of all identities currently represented by the
__agent__ without using a magnifying glass.

However in some case the __agent__ is just too secretive and will not
talk to us:

```bash
ssh-add -l
Could not open a connection to your authentication agent.
```

To change his mind, we invoke him and execute his answer with an `eval` (or
similar) command. It works like [Eliza](https://en.wikipedia.org/wiki/ELIZA)
you reflect the communication back and as you see, commination with an
__agent__ is two dimensional.  The `eval` command basically sets the variables:
`SSH_AUTH_SOCK` and `SSH_AGENT_PID` so that the `ssh` commands knows how to
talk to the __agent__.  It depends on the ticks, if you do no use a back-tick
you will get.

```bash
 eval 'ssh-agent'
SSH_AUTH_SOCK=/tmp/ssh-fMGM9OrtVYR4/agent.990266; export SSH_AUTH_SOCK;
SSH_AGENT_PID=990267; export SSH_AGENT_PID;
echo Agent pid 990267;
```

This will not update the `ssh-agent` and the connection will be refused. If you
are using back-ticks it looks different.

```bash
eval `ssh-agent`
Agent pid 990667
```

If you do not like back-ticks, other approaches also work. There seems to be no
difference if you use the '-s' or not, even if this option is used in other
examples online. The '-s' options generate Bourne shell commands on `stdout`.
This is the default if `SHELL` does not look like it's a csh style of shell.
So it is better to let the __agent__ guess the shell and only if you are
smarter than the __agent__ tell him how to speak (csh or Bourne).


```bash
eval $(ssh-agent)`
Agent pid 5079
ssh-add -l
The agent has no identities.
```

Now we can add an identity.

```bash
ssh-add ~/.ssh/id_ed25519_github
Identity added: /home/$USER/.ssh/id_ed25519_github ($USER@$HOST.$TLD)
ssh-add -l
256 SHA256:Cekfa54+saeceiR4ooVegXGYqu+Veixae7rooroDefs $USER@$HOST.$TLD (ED25519
```

After it is clear that the __agent__ is using the correct key, one could test
the access to <https://gitub.com> for example.

```bash
ssh -vT git@github.com
```

You could of course tell `ssh` directly what key to use, and bypass the
__agent__ entirely, if you do __not__ like `ssh` to talk to him. Add this to
your `~/.ssh/config`.


```ssh
Host github.com
 HostName github.com
 IdentityFile ~/.ssh/id_ed25519_github
```

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.0   | 2022-06-22 | Initial release                                      |



