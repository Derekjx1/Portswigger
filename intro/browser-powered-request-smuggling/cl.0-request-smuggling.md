# CL.0 request smuggling

## What is CL.0?

* The `Content-Length` header describes the size of body, usually indicates where request ends
* If the front-end still uses the `Content-Length` header to determine the end of the request, while the back-end ignores it(effectively rendering content-length: 0), an attacker can potentially exploit this discrepancy.&#x20;

## Testing for CL.0

* By sending a request that contains our smuggled payload such as a arbitary endpoint in the body.
* Finding an endpoint that accepts the POST request (Endpoint that does not expect POST but still able to accept and returns valid response)
* Making sure that connection is reused and  connection is kept-alive&#x20;
* Sending subsequent normal request, check if the response was triggered by the smuggled prefix
* If response is affected by smuggled prefix, high chance that backend server is ignoring the CL header.
* If server is downgrading HTTP2 to HTTP1, we can potentially exploit the same behaviour

{% hint style="info" %}
**Tip:** Note that the Content-Length header should accurately reflect the exact size of the payload, making this a completely legitimate request.
{% endhint %}

<figure><img src="../../.gitbook/assets/image (32).png" alt=""><figcaption><p>Sending POST request with smuggled paylaod to vulnerable endpoint</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (75).png" alt=""><figcaption><p>Sending subsequent request results in an error as expected</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (77).png" alt=""><figcaption><p>Sending a malicious payload to delete user</p></figcaption></figure>
