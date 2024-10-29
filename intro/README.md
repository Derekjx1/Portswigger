---
description: Learning points about HTTP request smuggling
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# HTTP Request Smuggling

## What is HTTP request smuggling? <a href="#what-is-http-request-smuggling" id="what-is-http-request-smuggling"></a>

HTTP request smuggling is a technique for <mark style="color:yellow;">interfering with the way a web site processes sequences of HTTP requests that are received from one or more users</mark>. Request smuggling vulnerabilities are often critical in nature, allowing an attacker to bypass security controls, gain unauthorized access to sensitive data, and directly compromise other application users.

## How does HTTP Smuggling work?

Modern web applications today employ several http servers between user and application logic. Users send request to front end servers and forwards requests to one or more backend servers for processing. This kind of web architecture is very common in the modern day web infrastructure.&#x20;

Requests sent between the frontend and backend servers usually rely on the same backbone connection. HTTP requests are sent one after another and the headers tell the servers when and where the requests start and end. There for the whole point of HTTP smuggling is to cause a desync between the frontend and backend.

In HTTP request smuggling, attackers will try to smuggle malicious requests by making ambiguous requests where the frontend and backend servers don't agree on where the start and end of request lies. Usually the `content-length` and `transfer-encoding` headers would determine this.

There are some scenarios how servers behaviour using this 2 headers.

* CL.TE: the front-end server uses the `Content-Length` header and the back-end server uses the `Transfer-Encoding` header.
* TE.CL: the front-end server uses the `Transfer-Encoding` header and the back-end server uses the `Content-Length` header.
* TE.TE: the front-end and back-end servers both support the `Transfer-Encoding` header, but one of the servers can be induced not to process it by obfuscating the header in some way.
