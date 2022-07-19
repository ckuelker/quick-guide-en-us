---
title: TLS Certificates
linkTitle: Certificates
author: Christian Külker
date: 2022-07-19
version: 0.1.1
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Certificates
commands:
- telnet
- ssh
- openssl
- certbot
tags:
- Index
- TLS
- SSL
- Transport layer security
- Secure Socket Layer
- X.509
description: Certificates ensure trust

---

## Abstract

This document gives a non comprehensive very brief overview over open source
certification standards. Two standards are handled in specifically: (1) Self
signed certificates and (2) Let's encrypt certificates.

## Introduction

When it comes to encryption, of web services for example, a certificate is
used. The old term  __Secure Sockets Layer__ `SSL` certificate changed
to __Transport Layer Security__ [`TLS`] a while ago as the transport is not only
over sockets any more. `TLS` is a cryptographic protocol designed to provide
data security of a transport layer, for example a network. The `TLS` protocol
ensures __pivacy__ (confidentiality), __integrity__ and __authenticity__ via
certificates. It runs on the _application layer_ ([`OSI`] model layer 7)
itself and can be divided between _TLS record protocols_ and _TLS handshake
protocols_. The current (2022-07-19) version is `TLS` 1.3 from 2018.

[`TLS`]: https://en.wikipedia.org/wiki/Transport_Layer_Security
[`OSI`]: https://en.wikipedia.org/wiki/OSI_model

Software that uses `TLS` certificates for example:

- Apache, Apache2
- Calendarserver
- Dkim (mail)
- Ejabberd (Jabber)
- Firefox
- Gitlab
- Mumble
- Nginx
- Postfix
- Privacy-Idea (Authentication Server)
- Prosody (Jabber)
- Radicale (Calendar Server)

## Types/Provider of Certificates

In the early days of Unix most connections were not encrypted. In 1990 for
example `http` or `telnet` was often used. Later on, when it came apparent that
encryption needed to be added to the communication, `SSL` and later `TLS` was
added to the services. In the case of `telnet` it was not considered to be
feasible to add security, people switched to a completely different service:
`ssh`. In the case of the world wide web the `http` protocol over port 80 was
and is still used, however the switch to `https` on port 443 is more common
today, both handled by the same web server, Apache or Nginx for example. While
the `telnet` or `ssh` users of a server understand the trust relation to that
server and therefore use self signed certificates by the server, the users of
the world wide web do not, and in the beginning self signed certificated used
are now replaced by others. It is not the case that self signed certificates
are insecure. The level of protection is the same as certificates signed by
others. The reason why self signed certificates are less common for web
services nowadays is that it is and was always difficult to understand if the
certificate was actually issued by the web server or not. A trust problem. In
contrast to `ssh` where a user uses one server often, in the world of `https` a
user uses many different unknown servers per day and in may cases only once.
The checking and management of `TLS` certificates for web services, if done
manually, would take more time than to actually read the content of the web
page.  Therefore the trust in oneself is replaced by the trust to certain more
or less well known organizations that issue certificates on behalf.

__Types and providers of certificates__

1. Self signed certificate (`openssl`)
2. Open Source 3rd party certificates (Let's Encrypt)
3. Free to use 3rd party certificates (startssl.com)
4. Commercial 3rd party certificates

## Openssl Self Signed Certificates

Self signed are as secure as 3rd party signed certificates. The question is a
trust one: How do I know that the certificate is the correct certificate? The
problem is similar to the quality problem of some countries: If there is no
quality assurance of products, industry standards for example, people do not
know the degree of quality of a product. It is often seen that people of such
countries tend to buy brand products as they expect that it is less likely that
a product of a well known brand would be of less quality. This assumption is
not always justified.

However as the same effect applies to web certificates, some applications
(browser) are now refusing self signed certificates for cultural reasons, as
the user is not capable to read and judge the content of a self signed
certificate.

Self signed certificates can still be used in some services and for also for
testing web services in development. A very common method to create a self
signed certificate is to create a certificate authority (`CA`) and sign
certificates with this `CA` by a command called `openssl`.

## Let's Encrypt (openssl) aka ACMEv2

As commercial certificates have the same protection level as self signed ones,
but cost money and are not free (as in FOSS), there have been many approaches
over the years to implement a free (as in FOSS) and free (as in beer - without
money) open source service. A recent famous one is __Let's Encrypt__.

The Certificate Authority (`CA`) named  __Let's Encrypt__ is a non profit `CA`
run by the __Internet Security Research Group__ (`ISRG`) that provide `X.509`
certificates for `TLS` encryption without charge. So one can use this
certificates for web and mail servers for example.

Unlike previous approaches, __Let's  Encrypt__ uses the __automated certificate
management environment__ (`ACME`) aka `ACMEv2`
[RFC8555](https://tools.ietf.org/html/rfc8555). `ACMEv2` certifies the domain
name. Others like `ACME-IP` validates IP addresses. Certificate management
includes:

- Account Creation
- Ordering a Certificate
- Identifier Authorization
- Certificate Issuance
- Certificate Revocation

One of the problem of previous approaches of certificate distribution was that
the certificate gets very old. Some system administrators therefor extend the
validity time of certificates to decades, as it was labor to renew a
certificate. Let's Encrypt solved the distribution problem and new certificates
are rolled out approximately every 3 month.

### Challenges

`ACMEv2` defines challenges. The `HTTP` challenge proves that the admin as
resource control of a domain (web server). The `DNS` challenges proves the
control of a specific domain name (`DNS` server). For example a `DNS` entry
like this:

    _acme-challenge.www.example.org. 300 IN TXT "gfj9Xq...Rg85nM"

This should be removed after challenge is over (status: valid, invalid)

It is recommended that DNS servers use `DNSSEC` [`rfc8555` p. 87].

However, the most common challenges is the `HTTP` challenge. For this __port
80__ needs to be open or used by a web server: Nginx or Apache2. The method is
similar to the `DNS` challenge. Information is added to the web server to prove
that the web server is under control of the script that requests the new
certificate. When the new certificate was received, the information is removed
and also the communication is changed from not-encrypted to encrypted. The
complex process can be done manually, however there are scripts that can take
over this task. They are called `ACMEv2` clients.

### Clients

There are many `ACMEv2` compatible [clients], Let's Encrypt recommends
[`certbot`] (Certbot >= 0.22.0 - for `ACMEv2`).

Other clients are for example:

| Server | Language | ACMEv2 | Wild Card | Client           |
| ------ | -------- | ------ | --------- | ---------------- |
| nginx  | Python   | yes    | yes       | [acme-nginx]     |
| nginx  | Go       | ?      |           | [ngxpkg]         |
| nginx  | Perl     | yes    | yes       | [Crypt::LE]      |
|        | Perl     | no     | ?         | [Net::ACME]      |
|        | Perl     | yes    | ?         | [Net::ACME2]     |
|        | Perl     |        |           | [Protocol::ACME] |

[clients]: https://letsencrypt.org/docs/client-options/
[`certbot`]: https://certbot.eff.org/
[acme-nginx]: https://github.com/kshcherban/acme-nginx
[ngxpkg]: https://github.com/webpkg/ngxpkg
[Crypt::LE]: https://github.com/do-know/Crypt-LE
[Net::ACME]: https://metacpan.org/pod/Net::ACME
[Net::ACME2]: https://metacpan.org/pod/Net::ACME2
[Protocol::ACME]: https://metacpan.org/pod/Protocol::ACME

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.1   | 2022-07-19 | Updates for quick guide                              |
| 0.1.0   | 2020-01-31 | Initial release                                      |

