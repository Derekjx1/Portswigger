# Blind SQLI

## Testing for Blind SQLI using conditional responses

Usually blind sqli is commnonly used when situations where there is no visible or obvious message responses.

Using conditional payloads such as 1=1 or 1=2, we can observe how application is reacting to these true or false statements. Through indirect response such as page content, response length it is possible to infer if the application is vulnerable,for example in situations like password enumeration.



## Error-Based 

Injecting true or false statements using payloads such as 1=1 and 1=0 is in some cases might not work because application behaviour doesn't change when evaulating these boolean payloads. However, we can purposely induce error messages with payloads when a certain condition is true.(e.g. syntax error etc).


<ins> Error-based SQLi using conditional statements</ins>

