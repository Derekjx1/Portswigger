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

# HTTP/2 Request smuggling via CRLF injection

There are some http/2 exclusive inputs that can construct high severity exploits even though front-end and back-end does not reuse the same TCP connection.&#x20;

Sometimes there we can create discrepancies even though frontend can validate content-length headers or even striping transfer-encoding headers, this often can be circumvented. Such as the discrepancies of using the `/n(newline)` syntax.

Frontend may not treat this as a delimiter but backend using HTTP/1 protocol does. Using the full CRLF(/r/n),we are able to circumvent this because all servers using HTTP/1 agree that CRLF terminates the header\
![](<../../../.gitbook/assets/image (72).png>)

HTTP/2 messages are <mark style="color:yellow;">binary rather than text-based</mark>, the boundaries of each header are based on explicit, predetermined offsets rather than delimiter characters.  Therefore the `/r/n` value does not hold significant value and can be used within the header, the danger comes when HTTP/2 is downgraded and this is interpreted as two headers by HTTP/1.

### Injecting via header names <a href="#injecting-via-header-names" id="injecting-via-header-names"></a>

For HTTP/1 , it is not possible to put a colon(:) into header names because it is used as a special character to indicate end of header names. However, it is not the case with HTTP/2 protocol. Hence, we can inject arbitary header names that we want by using the `\r\n` syntax with the colon and these will be interpreted as header names for the backend using HTTP/1

<figure><img src="../../../.gitbook/assets/image (48).png" alt="" width="331"><figcaption></figcaption></figure>

