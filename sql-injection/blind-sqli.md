# Blind SQLI

## Testing for Blind SQLI using conditional responses

Usually blind sqli is commnonly used when situations where there is no visible or obvious message responses.

Using conditional payloads such as 1=1 or 1=2, we can observe how application is reacting to these true or false statements. Through indirect response such as page content, response length it is possible to infer if the application is vulnerable,for example in situations like password enumeration.



## Error-Based 

Situations where application response with database errors. Through interpreting error messages, we may be able to extract meaningful information from application. Sometimes, when there is no visible responses or behaviour, conditional statements can be used to induce error messages as well.

<ins> Error-based SQLi using conditional statements</ins>
