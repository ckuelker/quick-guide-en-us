---
title: TLS Certificates
linkTitle: Certificates
author: Christian Külker
date: 2023-05-30
version: 0.1.2
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

This document provides a very brief, non-exhaustive overview of open source
certification standards. Two standards are discussed in particular: (1) Self
signed certificates and (2) Let's encrypt certificates.

## Introduction

When it comes to encryption, for example of web services, a certificate is
used. The old term __Secure Sockets Layer__ [`SSL`] certificate changed to
__Transport Layer Security__ [`TLS`] some time ago, because the transport is
not only done over sockets anymore. TLS is a cryptographic protocol designed to
provide data security over a transport layer, such as a network. The TLS
protocol guarantees _privacy__ (confidentiality), _integrity__ and
_authenticity__ via certificates. It runs on the _application layer_ ([`OSI`]
model layer 7) itself and can be divided into _TLS record protocols_ and _TLS
handshake protocols_. The current (2022-07-19) version is `TLS` 1.3 from 2018.

[`TLS`]: https://en.wikipedia.org/wiki/Transport_Layer_Security
[`OSI`]: https://en.wikipedia.org/wiki/OSI_model

Software that uses `TLS` certificates, for example:

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

In the early days of Unix, most connections were not encrypted. For example, in
1990, `http` or `telnet` was often used. Later, when it became apparent that
encryption was needed, `SSL` and later `TLS` were added to the services. In the
case of `telnet`, it was not considered feasible to add security, so people
switched to a completely different service: `ssh`. In the case of the World
Wide Web, the `http` protocol on port 80 was and still is used, but the switch
to `https` on port 443 is more common today, both handled by the same web
server, for example Apache or Nginx. While the `telnet` or `ssh` users of a
server understand the trust relationship to that server and therefore use
self-signed certificates from the server, the users of the World Wide Web do
not, and the self-signed certificates used initially are now being replaced by
others. It is not the case that self-signed certificates are insecure. The
level of protection is the same as certificates signed by others. The reason
why self-signed certificates are less common for web services today is that it
is and has always been difficult to understand whether the certificate was
actually issued by the web server or not. A trust issue. Unlike `ssh`, where a
user often uses one server, in the `https` world a user uses many different
unknown servers per day, and in some cases only once.  Checking and managing
`TLS` certificates for web services, if done manually, would take more time
than actually reading the content of the web page.  Therefore, trusting
yourself is replaced by trusting certain more or less known organizations that
issue certificates on your behalf.

__Types and providers of certificates__

1. Self signed certificate (`openssl`)
2. Open Source 3rd party certificates (Let's Encrypt)
3. Free to use 3rd party certificates (startssl.com)
4. Commercial 3rd party certificates

## Openssl Self Signed Certificates

Self-signed certificates are as secure as third-party-signed certificates. The
question is one of trust: How do I know the certificate is the right
certificate? The problem is similar to the quality problem in some countries:
If there is no quality assurance of products, such as industry standards,
people do not know the level of quality of a product. It is often seen that
people in such countries tend to buy branded products because they expect that
a product of a well-known brand is less likely to be of inferior quality. This
assumption is not always correct.

However, since the same effect applies to web certificates, some applications
(browsers) now refuse self-signed certificates for cultural reasons, since the
user is not able to read and judge the content of a self-signed certificate.

Self-signed certificates can still be used in some services and for testing Web
services during development. A very common way to create a self-signed
certificate is to create a Certificate Authority (`CA`) and sign certificates
with that `CA` using a command called `openssl`.

## Let's Encrypt (openssl) aka ACMEv2

Since commercial certificates provide the same level of protection as
self-signed certificates, but cost money and are not free (as in FOSS), there
have been many attempts over the years to implement a free (as in FOSS) and
free (as in beer - without money) open source service. A recent famous one is
__Let's Encrypt__.

The Certificate Authority (`CA`) called __Let's Encrypt__ is a non-profit `CA`
run by the __Internet Security Research Group__ (`ISRG`) that provides `X.509`
certificates for `TLS` encryption free of charge. You can use these
certificates for web and mail servers.

Unlike previous approaches, __Let's Encrypt__ uses the __Automated Certificate
Management Environment__ (`ACME`), also known as `ACMEv2`
[RFC8555](https://tools.ietf.org/html/rfc8555). ACMEv2 certifies the domain
name. Others such as `ACME-IP` validate IP addresses. Certificate management is
included:

- Account Creation
- Ordering a Certificate
- Identifier Authorization
- Certificate Issuance
- Certificate Revocation

One of the problems with previous approaches to certificate distribution was
that the certificate got very old. As a result, some system administrators
extend the validity of certificates for decades because it is laborious to
renew a certificate. Let's Encrypt solved the distribution problem and new
certificates are rolled out approximately every 3 months.

### Challenges

ACMEv2 defines challenges. The `HTTP` challenge proves that the admin is the
resource controller of a domain (web server). The `DNS` challenge proves
control of a specific domain name (`DNS` server). For example, a `DNS` record
like this:

    _acme-challenge.www.example.org. 300 IN TXT "gfj9Xq...Rg85nM"

This should be removed after the challenge is over (status: valid, invalid).

It is recommended that DNS servers use `DNSSEC` [`rfc8555` p. 80].

However, the most common challenge is the `HTTP' challenge. For this, __port
80__ must be open or used by a web server: Nginx or Apache2. The method is
similar to the `DNS' challenge. Information is added to the web server to prove
that the web server is under the control of the script requesting the new
certificate. When the new certificate is received, the information is removed
and the communication is changed from unencrypted to encrypted. This complex
process can be done manually, but there are scripts that can do the job for
you. They are called `ACMEv2` clients.

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
| 0.1.2   | 2023-05-30 | Improve writing                                      |
| 0.1.1   | 2022-07-19 | Updates for quick guide                              |
| 0.1.0   | 2020-01-31 | Initial release                                      |

