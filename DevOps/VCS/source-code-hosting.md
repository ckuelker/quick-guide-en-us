---
title: Source Code Hosting
linkTitle: Source-Code-Hosting
author: Christian Külker
date: 2024-02-12
version: 0.1.2
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- VCS Hosting
commands:
- git
- svn
- cvs
tags:
- Hosting
- Source-Code-Hosting
description: Source Code Hosting

---

## Introduction

Source code hosting is a fundamental service that organizations and projects
must consider for effective management. While some newer Version Control
Systems (_VCS_), such as _Git_, incorporate built-in hosting features, scaling
access management often requires source code servers. These servers act as
intermediaries between developers and repositories. For _VCS_ like _SVN_ or
_CVS_, centralized source code management was the sole option. However, _Git_
and similar systems are decentralized. Manual management of these distributed
repositories becomes overly complex as an organization or project expands.
Despite _Git's_ decentralized nature, most organizations and projects opt for a
centralized server infrastructure. The choice between onsite hosting or
third-party hosting depends on the organization's IT infrastructure and the
confidentiality of the source code. Third-party hosting often involves
platforms like <https://github.com>, though numerous solutions exist for both
third-party and self-hosting as Free Open Source Software (_FOSS_). This
document compiles Free Open Source Software options for hosting source code
projects.

*[VCS]: Version Control Systems

*[SVN]: Subversion

*[CVS]: Concurrent Version System

*[FOSS]: Free Open Source Software

[VCS]: https://en.wikipedia.org/wiki/Version_control
[SVN]: https://en.wikipedia.org/wiki/Apache_Subversion
[CVS]: https://en.wikipedia.org/wiki/Concurrent_Versions_System
[FOSS]: https://en.wikipedia.org/wiki/Free_and_open-source_software

## Overview

**FOSS VCS**

- Git
- Subversion (SVN)
- Mercurial
- Concurrent Version System (CVS)

**FOSS Hosting Software**

- GNU Savannah
- GitBucket
- Gitlab
- Gogs
- Gitea
- GitPrep (Github clone)
- Kallithea [Git, Mercurial] (GPLv3)
- TuleapL
- Phabricator [Mercurial]
- PROPRIETARY + Community edition: RhodeCode [SVN, Git, Mercurial]
- Gitolite
- Gerrit

|               | Gitlab | Gogs | Gitea  | Gitolite |
| ------------- | ------ | ---- | ------ | -------- |
| Fork of       |        |      | Gogs   |          |
| Written in    |        | Go   | Go     |          |
| Web interface | yes    |      | yes    | no       |
| Database      |        | yes  | yes    |          |

## Gitlab

- Home: <https://about.gitlab.com/>
- Source: <https://gitlab.com/gitlab-org/gitlab>
- Documentation: <https://about.gitlab.com/>
- Guide: <https://about.gitlab.com/get-started/>

## Gogs

- Home: <https://gogs.io/>
- Source: <https://github.com/gogs/gogs>
- Documentation: <https://gogs.io/docs>

### Databases

- mysql
- pq
- sqlite3
- mssqldb

## Gitea

- Home: <https://gitea.io/en-us/>
- Source: <https://code.gitea.io/gitea/>
- Documentation: <https://docs.gitea.io/en-us/>

### Databases

- MySQL (>=5.7)
- PostgreSQL (>=10)
- SQLite3
- MSSQL (>=2008R2 SP3)
- TiDB (MySQL protocol)

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2024-02-12 | Fix acronym definition (not supported by pandoc)     |
| 0.1.1   | 2024-02-04 | Add project chapters                                 |
| 0.1.0   | 2022-06-21 | Initial release                                      |
