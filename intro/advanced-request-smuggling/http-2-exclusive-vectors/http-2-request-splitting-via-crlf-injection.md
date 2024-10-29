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

# HTTP/2 Request Splitting via CRLF injection

Request splitting is smiliar to the [HTTP response queue poisoning](../response-queue-poisoning.md).  In the case of HTTP/1, the splitting occurs in the request body but in HTTP/2 because of downgrading, the splitting can also occur in the headers.

<figure><img src="../../../.gitbook/assets/image (49).png" alt=""><figcaption><p>Injecting a arbitary header "foo" to our http/2 request containing our smuggled request.</p></figcaption></figure>

We send a abritary header containing our smuggled request payload with a host header and the /admin endpoint that we want to access.  We may get 401 error when trying the first few request, after sending more requests and poisoning the response queue other users may get our 401 responses. Thus creating a desync of responses.&#x20;

Here, we successfully intercepted a 302 page directing us to a administrator account along with a session cookie.&#x20;

Now by sending another request using the adminitrator's session cookie we are able to access the account and delete users.

<figure><img src="../../../.gitbook/assets/image (50).png" alt=""><figcaption><p>Sending a HTTP GET to the  /my-account endpoint and the id parameter containing adminsitrator and using its session cookie.</p></figcaption></figure>





