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

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption><p>Normal request that sends api request to backend to query inventory system</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption><p>Malicious request that tells server to get the admin interface on localhost</p></figcaption></figure>


# Bypassing poor SSRF defenses that filters blacklisted keywords

Some poorly configured SSRF defenses that blocks certain keywords such as localhost, can potentially be bypass. Using url encoding or obfuscating the payload. 
![image](https://github.com/user-attachments/assets/c6ca93f4-bf16-4577-8d91-9294f73f3c52)



# SSRF filter bypass using open redirect vulnerability

SSRF vulnerabilities can still be exploited even though there are filters such as a blacklist or a whitelist of keywords such as domain names. Suppose that a stock check feature of an application is calling out to backend api has defenses that 
checks the supplied URL but supports redirection functionality. If a application has a open redirect vulnerability, the URL here may potentially be used as the payload to exploit SSRF.

Input validation on supplied URL
![image](https://github.com/user-attachments/assets/e085aa2d-be16-4a2a-9a2c-5ab7988be35a) 

Open redirect vulnerability on next product functionality
<img width="921" alt="image" src="https://github.com/user-attachments/assets/6401ddf0-ab70-4a9d-a81a-1ed020e4dd09" />

Using open redirect to exploit SSRF
![image](https://github.com/user-attachments/assets/f24382d2-5102-4285-9d02-79d05a8d813c)


> This works because the supplied URL is being allowed
