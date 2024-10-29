---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: false
  outline:
    visible: false
  pagination:
    visible: true
---

# Using HTTP request smuggling to turn an on-site redirect into an open redirect

Normal web applications will have a usually perform onsite URL redirects. They often use the hostname header and include it in the redirect URL location.

`GET /home HTTP/1.1` \
`Host: normal-website.com`

`HTTP/1.1 301 Moved Permanently` \
`Location: https://normal-website.com/home/`

This can be exploited by HTTP smuggling to redirect users to attacker's website

`POST / HTTP/1.1` \
`Host: vulnerable-website.com` \
`Content-Length: 54` \
`Transfer-Encoding: chunked`

`0`

<mark style="color:yellow;">`GET /home HTTP/1.1`</mark> \ <mark style="color:yellow;">`Host: attacker-website.com`</mark> \ <mark style="color:yellow;">`Foo: X`</mark>

## Using HTTP request smuggling to perform web cache poisoning <a href="#using-http-request-smuggling-to-perform-web-cache-poisoning" id="using-http-request-smuggling-to-perform-web-cache-poisoning"></a>

If front end infrastructure does some type of caching , poisoning of the cache may be possible.

<figure><img src="../.gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>

A simple test shows that the response from the smuggled request on the `/post/next?postId=2` will result in redirecting us to the path using the location header containing our arbitrary value on the host header in the smuggled request. Therefore, we can modify the header here to inject our exploit server value here to redirect users to our server instead.

<figure><img src="../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

This results in the backend server responding to a redirect to the exploit server and the frontend caches the response for what it believes is from the second URL which is `/resources/js/tracking.js`

<figure><img src="../.gitbook/assets/image (60).png" alt=""><figcaption></figcaption></figure>

Anybody who requests this endpoint `/resources/js/tracking.js` will be redirected to the attcker's server until the cache expires.

## Using HTTP request smuggling to perform web cache deception <a href="#using-http-request-smuggling-to-perform-web-cache-deception" id="using-http-request-smuggling-to-perform-web-cache-deception"></a>

Web cache poisoning and deception works in a similar way but with a different purpose.

* Web cache **poisoning** cause application to store malicious content in the cache which is served to normal users when they access it
* Web cache **deception** causes application to store sensitive content from other users in the cache and attacker retrieves it from cache

<figure><img src="../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

In our HTTP request, we try to smuggle a GET request to try to fool the front end to cache the my-account page from the victim. Once the front end has cache the static endpoints, we just need to send a request to the static endpoint that is being cache and we can retrieve the api key from the victim's session

<figure><img src="../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>
