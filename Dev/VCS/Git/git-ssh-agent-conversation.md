---
title: Git Conversations With The SSH Agent Conversation
linkTitle: Git-SSH-Agent-Conversation
author: Christian Külker
date: 2023-05-05
version: 0.1.1
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
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

When using the `ssh` command, `ssh` uses a key stored in `~/.ssh` and you
rarely need to worry about the details. Recently, however, in the context of
moving identities and permissions away from passwords, `ssh` __public__ keys
are increasingly being used to control access. This is also the case with
`git`.  While on most systems this is handled transparently, sometimes the
wrong key is used and access is denied when it should not be. This document
covers `ssh` key management, __agents__ and `git`.

For the sake of education, let us assume that we have generated a private and
public key that will only be used to access a commercial site under `~/.ssh`
called `id_ed25519_github`. So we have the following files:

- `~/.ssh/id_ed25519_github` (private key - we never touch this!)
- `~/.ssh/id_ed25519_github.pub` (public key)

As we are in a dangerous world to help with this kind of things we need an
__agent__.  Luckily `SSH` provides such an __agent__ called `ssh-agent`.

Usually this __agent__ is already started on your favorite operating system:
Debian. It is very common to start the __agent__ together with the `X` session
aka desktop.  However, for some reason unknown to me, it is usually not very
talkative (or dormant if `X` is not started), at least when using the system
remotely.

To understand if our __agent__ is alive and willing to communicate with a
mortal, you can see if the __agent__ is listed in the process list:

```bash
ps ax|grep ssh-agent|grep -v grep
  33178 ?        Ss     0:02 /usr/bin/ssh-agent x-session-manager
```

After understanding that we have an __agent__ alive, we could ask him for our
keys, oddly we do not execute `ssh-agent ask` or something, but `ssh-add`, even
though we are not adding anything. Who would have thought?

```bash
ssh-add -l
```

This will list the fingerprints of all identities currently represented by the
__agent__ without using a magnifying glass.

However, in some cases the __agent__ is just too secretive and will not talk to
us:

```bash
ssh-add -l
Could not open a connection to your authentication agent.
```

To change his mind, we invoke him and execute his answer with an `eval` (or
similar) command. It works like [Eliza](https://en.wikipedia.org/wiki/ELIZA),
you reflect the communication back, and as you can see, communication with an
__agent__ is two-dimensional.  The `eval` command basically sets the variables:
`SSH_AUTH_SOCK` and `SSH_AGENT_PID` so that the `ssh` commands know how to talk
to the __agent__.  It depends on the ticks, if you do not use a back-tick you
will get:

```bash
 eval 'ssh-agent'
SSH_AUTH_SOCK=/tmp/ssh-fMGM9OrtVYR4/agent.990266; export SSH_AUTH_SOCK;
SSH_AGENT_PID=990267; export SSH_AGENT_PID;
echo Agent pid 990267;
```

This will not update the `ssh-agent` and the connection will be denied. If you
are using back-ticks, the situation is different.

```bash
eval `ssh-agent`
Agent pid 990667
```

If you do not like backticks, there are other approaches. There seems to be no
difference between using the `-s` or not, even if this option is used in other
examples online. The `-s` options generate Bourne shell commands on `stdout`.
This is the default when `SHELL` doesn't look like a csh-style shell.  So it is
better to let the __agent__ guess the shell and only if you are smarter than
the __agent__ tell him how to speak (csh or Bourne).


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

Once it is clear that the __agent__ is using the correct key, you could test
access to <https://gitub.com>, for example.

```bash
ssh -vT git@github.com
```

Of course, you could tell `ssh` directly what key to use, and bypass the
__agent__ entirely if you __don't__ want `ssh` to talk to him. Add this to your
`~/.ssh/config`.

```ssh
Host github.com
 HostName github.com
 IdentityFile ~/.ssh/id_ed25519_github
```

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2023-05-05 | Improve writing                                      |
| 0.1.0   | 2022-06-22 | Initial release                                      |



