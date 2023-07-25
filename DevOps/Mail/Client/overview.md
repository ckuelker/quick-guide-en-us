---
title: Mail Client Overview
linkTitle: Overview
author: Christian Külker
date: 2023-07-25
version: 0.1.2
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- E-Mail-Client
commands:
- mutt
tags:
- Index
description: Comparison of e-mail clients

---

The number of email clients under GNU/Linux is quite large, as email has been a
very common way of federated communication under Unix from the very beginning.
In the old days, every Unix or Linux machine was basically a global attached
mail server, and therefore IMAP and POP were not that common, as it was easy to
read local mail on the mail server using a terminal. Nowadays the usage has
shifted from local mail storage on the mail server to remote storage on the
mail server, read by a client computer (via IMAP or POP3) connected via a more
or less permanent Internet connection, as the role of computer client and
server has diverged. There are some flavors of handling (like IMAP or POP3) or
the question of mail storage (storage only on the server via IMAP, or storage
on the server and the computer client via fetching mails).

While it makes sense for a laptop to fetch all mail or a subset of new mail and
store it locally, because the Internet may not always be available, a
permanently network attached desktop computer might not want to store all mail
locally.

In addition, it is more common today to manage more than one email address, or
even more than one identity, and in some cases even subfolders for accounts and
complex filtering rules. While some graphical clients handle all these tasks
quite well, the default setup of command line email clients may be able to do
the same, but suffer from the paradigm of handling one mail account, with one
identity, with only one inbox without subfolders, while the paradigm of
graphical clients is to handle one or more accounts, with one or more
identities, and usually they expect subfolders.

This paradigm shift can be seen in the layout. If you want to see a list of
mail from the inbox and a list of subfolders of the inbox, you would normally
use a 2-frame layout. In the left frame a list of subfolders and in the right
frame a list of emails. Command line clients often have only one frame (like
`alpine`, `sup`, `mu4e` and `lumail`), and some clients have a second frame as
an afterthought, which is cumbersome to access. Unfortunately, `mutt` and
`neomutt` give me this impression. They have a 2-frame layout, but with a
default setup are not easily used to browse the IMAP subfolders. What is
standard in graphical mail clients is called _advanced_ usage in `neomutt` for
example.

While clients like `mutt` or `neomutt` have many more features than graphical
clients, some basic features are not well implemented. For example, the
`account hook` of `neomutt` cannot be used to manage different identities of
IMAP accounts, because the `from` field cannot be set reliably via the `account
hook`. Changing a mailbox may not change the identity. Of course, you could say
that this is all a matter of configuration. Sure, but the learning curve here
is quite steep.

|                | mutt              | neomutt             | luamail |
| -------------- | ----------------- | ------------------- | ------- |
| In Debian 12   | 2.2.9-1+b1        | 20220429+dfsg1-4.1  | no      |
| In Debian 11   | 2.0.5-4.1+deb11u2 | 20201127+dfsg.1-1.2 | no      |
| Latest version | 2.2.9             | 20220429            | 3.1     |
| Latest update  | 2022              | 2023                | 2017    |


- [Lumail](lumail.md)
- [mutt](mutt.md) <http://www.mutt.org/>
- [neomutt](neomutt.md) <https://neomutt.org/>

### Neomutt

- [Code](https://github.com/neomutt/neomutt)

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2023-07-25 | Add versions for Debian 12 (bookworm)                |
| 0.1.1   | 2023-01-19 | Improve writing, add more version numbers            |
| 0.1.0   | 2022-07-17 | Initial release                                      |

