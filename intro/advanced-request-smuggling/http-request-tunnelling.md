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

# HTTP request tunnelling

Normal HTTP request smuggling works because of the underlying same TCP connection reuse for multiple request-responses. However, when this is not possible, HTTP request tunnelling can be used to achieve high severity exploits as well.  This is because <mark style="color:yellow;">HTTP smuggling relies on connection reuse whereas HTTP request tunneling works even without it</mark>.&#x20;

### Request tunnelling with HTTP/2 <a href="#request-tunnelling-with-http-2" id="request-tunnelling-with-http-2"></a>

Due to the way how keep-alive connections works in HTTP/1, we can't be sure if we have smuggled a request even if we received 2 responses. However, in HTTP/2 every stream would only contain a request and response if we receive a reponse containing a HTTP/1 body we can be sure that we have tunneled a second request.&#x20;

{% hint style="info" %}
To put it simply,&#x20;

1. **Request smuggling** is like putting two messages inside a letter and sending this to the receiver who might get confused at the message as they normally would see only one  message.
2. **Request tunneling** is  like putting a real message,fold it and put it inside a greeting card and then sending it to the receiver.
{% endhint %}



### Leaking internal headers using request smuggling

Front end and backend servers agree that there's only a single request. But due to the injection of arbitary headers, we can make them disagree where the headers end.  Frontend sees everything including the injected strings as headers while backend sees `\r\n\r\n` and takes it as where the headers end, leaving out the `comment=`.

<figure><img src="../../.gitbook/assets/image (64).png" alt=""><figcaption><p>Single HTTP request containing injection of arbitary headers  </p></figcaption></figure>

The backend server treats `comment=` as part of the body parameter and therefore internal headers will be appended to it as the value&#x20;

<figure><img src="../../.gitbook/assets/image (65).png" alt=""><figcaption><p>Backend treats trailing comment= as part of body</p></figcaption></figure>

### Blind request tunnelling <a href="#blind-request-tunnelling" id="blind-request-tunnelling"></a>

Blind request tunnelling is a type of HTTP request tunnelling that occurs without knowing the exact size of the response from the legitimate request.&#x20;

Some frontend read all the data from backend servers, When a request is tunneled, we could potentially receive both reponses forwarded to the client with <mark style="color:yellow;">response to the tunneled request seen in body</mark>. However, some front end servers only reads the size of the data determined by content-length of the reponse and forwards part of the response to the client resulting in blind request tunneling.

search parameter needs to be provided so as to complete the request structure to prevent sending malformed requests without access to the body

### Non-blind request tunnelling using HEAD

<figure><img src="../../.gitbook/assets/image (69).png" alt=""><figcaption><p>Reflect  frontend adding addtional headers to request when its send to backend</p></figcaption></figure>

We can inject the client authentication headers that were reflected in our malicious payload pointing to the /admin endpoint. However, depending on the path that was being requsted in our main request, we may need to adjust accordingly because the size of the response from the /admin page might be lesser or more than the main path that is going to be rendered, results will be truncated or will encounter a timeout.\


<figure><img src="../../.gitbook/assets/image (70).png" alt=""><figcaption><p>Timeout encountered because / endpoint is expecting 9k bytes and the tunnelled request returns only 3k.</p></figcaption></figure>

### Web cache poisoning via HTTP/2 request tunnelling

Through non blind HTTP request tunneling, we can mix and match headers to the body of the response of another. If there are instances where unencoded user input can be reflected to us, we may be able to inject code (reflected xss) in places where code is not normally executed.\
&#x20;Application developers may also cache certain pages on the front-end to improve response times and we can potentially poison the cache to serve such responses to other users when they visit the cached pages.

<figure><img src="../../.gitbook/assets/image (51).png" alt=""><figcaption><p>Path psuedo header is vulnerable to CRLF injection</p></figcaption></figure>

HEAD Requests and Content-Length:

* **HEAD requests** are like peeking into a room without entering. They ask for only the headers of a response, not the full content (body).
* But even though HEAD responses don't have a body, they often include a **Content-Length header**. This header tells you how big the response body WOULD be if you made a GET request instead.

Front-End Server Mishandling:

* Some front-end servers, acting as intermediaries between users and back-end servers, have a bug.
* They see the Content-Length header in a HEAD response and mistakenly try to read that many bytes, even though there's no body to read.

Tunneling a Request:

* A skilled hacker might craft a special request that "tunnels" through the front-end server, reaching the back-end directly.

<figure><img src="../../.gitbook/assets/image (52).png" alt=""><figcaption><p>Tunnelling another request to a different endpoint using the HEAD technique</p></figcaption></figure>

To be able achieve reflected XSS, we would need to find an endpoint that is able to embed our payload and reflect it to the user. Here, we discover  we are able to reflect our payload through the location header when we send to the `/resources` endpoint&#x20;

<figure><img src="../../.gitbook/assets/image (53).png" alt=""><figcaption><p>Discover an endpoint that is able to reflect our XSS payload </p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (54).png" alt=""><figcaption><p>Adding our malicious XSS payload and padding characters to match the content length for our main GET request</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (55).png" alt=""><figcaption><p>Removing our cache buster and confirming the cache has been poisoning by visiting the home page</p></figcaption></figure>
