---
title: Simple Http Server
linkTitle: Simple-Http-Server
author: Christian Külker
date: 2023-01-18
version: 0.1.2
locale: en_US
lang: en
type: doc
disclaimer: True
toc: True
categories:
- Python
- Python2
- Python3
- Docker
commands:
- python
- python3
- openssl
tags:
- Simple-Http-Server
- http.server
- SSL
description: A simple HTML web server in python for projects

---

## Introduction

Sometimes it is handy to just have simple local web server that serves a web
directory in a non complex way. To be used in a web framework development, in a
web site development, or just for validating the HTML part of the project
documentation.

## (Simple) Http Server

### Python 3

As with `python3` the module has changed from `python2`: `http.server`.

``` {#mycode .bash .numberLines startFrom="1"}
python3 -m http.server 8000
```
Point your web browser to `http://127.0.0.1:8000` or `http://localhost:8000`.

If you want to be more explicit: [py-server-http](py-server-http)

```{#mycode .python .numberLines startFrom="1"}
import os
from http.server import HTTPServer, CGIHTTPRequestHandler
os.chdir('.') # Current directory, change it if needed
server_object = HTTPServer(server_address=('', 8000),
    RequestHandlerClass=CGIHTTPRequestHandler)
server_object.serve_forever()
```

In case SSL support is important:

1. Create key

``` {#mycode .bash .numberLines startFrom="1"}
openssl req -x509 -newkey rsa:2048 -keyout /path/to/key.pem \
-out /path/to/cert.pem -days 365
```

2. The SSL HTTP (HTTPS) server

```{#mycode .python .numberLines startFrom="1"}
import os
from http.server import HTTPServer, BaseHTTPRequestHandler
import ssl

os.chdir('.') # Current directory, change it if needed

httpd = HTTPServer(('localhost', 4443), BaseHTTPRequestHandler)

httpd.socket = ssl.wrap_socket (httpd.socket,
    keyfile="/path/to/key.pem",
    certfile='/path/to/cert.pem', server_side=True)

httpd.serve_forever()
```

Point your web browser to `https://127.0.0.1:4443` or `https://localhost:4443`.

### Python 3.9.2 with Docker

A simple web server can be used with Docker to expose `html` pages  to the
outside world for testing purposes.

```docker
FROM python:3.9.2

VOLUME ["/code"]
ADD . /code
WORKDIR /code

EXPOSE 5123
CMD ["python", "-m", "http.server", "5123"]
```

Point your web browser to `http://127.0.0.1:5123` or `http://localhost:5123`.


### Python 2

Go into the web directory you want to serve and execute `python2` with
`SimpleHTTPServer`.

``` {#mycode .bash .numberLines startFrom="1"}
python -m SimpleHTTPServer 8000
```

Point your web browser to `http://127.0.0.1:8000` or `http://localhost:8000`.

## History

| Version | Date       | Notes                                                |
| ------- | ---------- | ---------------------------------------------------- |
| 0.1.2   | 2023-01-18 | Improve writing, add SSL and Docker examples         |
| 0.1.1   | 2022-07-13 | Format for quick guide, update for python3           |
| 0.1.0   | 2016-07-19 | Initial release (as 0.1)                             |

