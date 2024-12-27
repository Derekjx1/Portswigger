# Server Side Request Forgery

## What is SSRF

#### Server-Side Request Forgery (SSRF) Vulnerabilities

Server-Side Request Forgery (SSRF) vulnerabilities allow attackers to instruct servers to make malicious requests to internal or external servers that may be otherwise inaccessible to them. By manipulating URLs or input parameters, attackers can trick the server into performing unauthorized actions or disclosing sensitive information.

## SSRF against local server or other backend systems

Causing the server making a http request hosting the application through its loopback interface,localhost or 127.0.0.1. Sometimes, application implicitly trusts the requests coming from a local machine which can be attributed to:

* Application control is managed in a different component which sit infront of app, checks can be bypassed when loopback request is made to server
* Disaster recovery, assumption that a fully trusted user is only able to access the system without authentication if they lose their credentials
* Administrative interface that is listening to another port from main app which might be accessible directly by user
* Other internal systems in the backend that users do not have direct acccess may be potential targets as well

<figure><img src=".gitbook/assets/image (15).png" alt=""><figcaption><p>Normal request that sends api request to backend to query inventory system</p></figcaption></figure>

<figure><img src=".gitbook/assets/image (16).png" alt=""><figcaption><p>Malicious request that tells server to get the admin interface on localhost</p></figcaption></figure>
