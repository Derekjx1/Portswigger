---
description: HTTP/2 can make applications even more vulnerable to request smuggling
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

# Advanced request smuggling

## HTTP/2 request smuggling <a href="#http-2-request-smuggling" id="http-2-request-smuggling"></a>

HTTP/2 encodes messages into a series of frames in a binary format and each frame has fixed headers that specifies the type, length, and stream identifier of the frame. The stream indentifier has a unique number that associates each frame with a request or response. The frontend and backend parse and indentify frames and corresponding messages without relying on the `content-length` and `transfer-encoding` headers like in HTTP/1.X. Keep in mind that HTTP/1.X uses textual messages to send and receive requests and responses.&#x20;

### HTTP/2 downgrading <a href="#http-2-downgrading" id="http-2-downgrading"></a>

HTTP/2 downgrading is the process of <mark style="color:yellow;">rewriting HTTP/2 messages using HTTP/1.X syntax</mark> to generate corresponding HTTP/1.X responses. Webservers/Reverse-proxy usually does this is support backend servers that are only HTTP/1.X enabled.\


<figure><img src="../../.gitbook/assets/image (33).png" alt="" width="375"><figcaption></figcaption></figure>

Each HTTP/1.X response has a HTTP/2 equilvalent, front-end servers are able to do this translation back for responses back to the client.
