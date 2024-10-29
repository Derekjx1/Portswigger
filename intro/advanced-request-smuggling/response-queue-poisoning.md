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

# Response queue poisoning

Response queue smuggling is a attack where attackers can steal arbirtary responses for victims and compromise their accounts or even the entire site.  This is being done by causing front-end requests being mapped to wrong reponses from the backend.&#x20;

This causes legitimate users to receive reponses that were intended for somebody else. This is acheived by smuggling a complete request, making the backend serve 2 responses where the front-end is only expecting one.

When response queue is posioned, attacker can capture other user's responses which can contain sensitive information like session tokens, cookies etc that enables attacker to takeover an account effectively. The website can also be brought down where legit users receiving random reponse from the server and breaking the flow.

## Pre-requisites for Response Queue Poisoning

1. <mark style="color:yellow;">TCP connection must be reused</mark> for mulitple request between frontend and backend communication
2. A <mark style="color:yellow;">complete HTTP request</mark> needs to be smuggled to the backend which gives a unique response&#x20;
3. <mark style="color:yellow;">Attack doesn't result in TCP connection close</mark> from either frontend or backend server.

## Constructing the Attack

<div align="left">

<figure><img src="../../.gitbook/assets/image (63).png" alt="" width="375"><figcaption></figcaption></figure>

</div>

* Typically request smuggling requires smuggling a partial request where server adds prefix to the next request
* Backend will see 2 request rather than 1 if smuggled request contains headers

If smuggled request contains a body, effectively the backend will see 3 requests instead where the third "request" are the leftover bytes that usually are invalid that induces the server to close the connection.

### Smuggling complete request

If we can embed two request in one, subsequent requests will not be affected and is unchanged.

![](broken-reference)

### Stealing other user responses

<div align="left">

<figure><img src="../../.gitbook/assets/image (66).png" alt="" width="375"><figcaption></figcaption></figure>

</div>

1. &#x20; Front-end see this as one request and forwards to backend. Backend sees 2 requests and generate 2 responses
2. Backend correctly maps first reponse to inital request and forwards back to client. The second reponse will be left hanging and is held as a queue on connection
3. The next request from client/frontend that comes to the backend server, the first response in the queue will be mapped to that request hence causing a desynchronization of reponses.
4. The correct response for that request will be left hanging without a matching request.This is repeated every time a new request is forwarded to backend.

<figure><img src="../../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

After the reponse queue is poisoned, attacker can send arbitary request to get other users responses. Although reponses cannot be controlled by attacker, using tools like burp intruder can quickly automate re-issuing of request to get a vast number of reponses intended for different user.

<figure><img src="../../.gitbook/assets/image (46).png" alt=""><figcaption><p>Upon sending our requests a few times, we got a response that was meant for the admin user</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (47).png" alt=""><figcaption><p>Using their session cookie that was in the response, we are able to hijack their session and gain access to the admin console to delete a user succesfully</p></figcaption></figure>

