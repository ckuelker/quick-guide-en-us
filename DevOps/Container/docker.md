---
title: Docker
linkTitle: Docker
author: Christian Külker
date: 2023-03-28
version: 0.1.1
type: doc
disclaimer: True
toc: True
categories:
- Container
tags:
- Docker
description: Docker

---

## Docker

Docker is an open source software mechanism for building, shipping, and running
applications. Docker allows you to separate your software applications from
your software infrastructure through an abstraction layer so you can deploy
software in a generalized way. With Docker, you can manage your servers the
same way you manage your applications. By taking advantage of Docker's methods
for rapidly shipping, testing, and deploying code, Docker promises to
significantly reduce the time between writing code and running it on a server
in a production environment.

Docker Engine can installed in different ways:

- Setup own Docker repository and install from them for the ease of
  installation and upgrade tasks. This is the recommended approach, except for
  Raspbian.
- Download DEB packages and install them manually and manage upgrades
  completely manually.
  This gives full cotrol on systems not connected to the internet.
- In testing and development environments scripts can install Docker in one
  shot. This is currently the most used approach for Raspbian.

=> https://docs.docker.com/engine/install/debian/

Membership in the docker group is more dangerous than sudo, according to the
[Debian Wiki] (https://wiki.debian.org/Docker). This is due to the `setUID`
root of the docker daemon and easy access to root. Access to docker commands
therefore effectively grants root access. Therefore, do not add users to the
docker group and use docker only with sudo.

### Editions

Docker comes in editions.

- ce: community edition
  - edge: montly release
  - stable: quarterly release
- ee: enterprise edition
  - quarterly release

### Debian 11 on ARM

The `docker.io` package contains the daemon and client. From the package
description:

> "Using docker.io on non-amd64 hosts is not supported at this time. Please be
> careful when using it on anything besides amd64."

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2023-03-28 | Improve writing, change cite format                  |
| 0.1.0   | 2020-11-05 | Initial release                                      |

