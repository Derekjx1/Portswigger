

# HTTP/2 exclusive vectors

## Injecting via pseudo-headers <a href="#injecting-via-pseudo-headers" id="injecting-via-pseudo-headers"></a>

HTTP/2 doesn't use a request line or status lines. Instead it uses a series of "pseudo-headers" to send key information about the message. These headers are prefixed with a colon to differentiate them from normal headers. There are five pseudo-headers in total:

* **:method**: The HTTP method of the request, such as GET or POST.
* **:path**: The request path. Note that this includes the query string.
* **:authority**: A stricter, mandatory information about the host authority (i.e. host name and host port).
* **:scheme**: The URI scheme of the request, such as http or https.
* **:status**: The HTTP status code of the response.&#x20;

Interestingly when websites downgrades to HTTP/1 , it uses some of these headers the create the request line.

### Supplying an ambiguous host <a href="#supplying-an-ambiguous-host" id="supplying-an-ambiguous-host"></a>

As HTTP/2 uses psuedo header `:authority` for the host header,we can still be allowed to supply a host header. This can end up be supplying 2 host headers and may even circumvent the duplictate host header error when downgrading to HTTP/1.\* Which can lead to host header attack.

### Supplying an ambiguous path&#x20;

In HTTP/1, it is not possible to send an ambiguous path but in HTTP/2 since psuedo headers are used we can send multiple paths. If there's a descrepancy in which path is validated by controls and which are used for routing, this may enable attackers to access endpoints that are off-limits.

<div align="left">

<figure><img src="../../../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>

</div>

### Injecting a full request line <a href="#injecting-a-full-request-line" id="injecting-a-full-request-line"></a>

Upon downgrading to HTTP/1 the `:method` will be written to the front of the request line. if server can tolerate spaces in the header, an totally different request may be injected.

<div align="left">

<figure><img src="../../../.gitbook/assets/image (56).png" alt="" width="563"><figcaption></figcaption></figure>

</div>

### Injecting a URL prefix <a href="#injecting-a-url-prefix" id="injecting-a-url-prefix"></a>

The `:scheme` normally only contains http or https however arbitrary values may be injected. When server is using `:scheme` to generate URL, we can potentially add a prefix or even completely overide the URL, pushing the legitimate path into a query string

<div align="left">

<figure><img src="../../../.gitbook/assets/image (61).png" alt="" width="375"><figcaption></figcaption></figure>

</div>

#### Injecting newlines into pseudo-headers <a href="#injecting-newlines-into-pseudo-headers" id="injecting-newlines-into-pseudo-headers"></a>

We can add newlines(`\r\n`) into the pseudo headers such as `:method` or `:path` so that we can get resulting valid HTTP/1 request
