# Blind SQLI

## Testing for Blind SQLI using conditional responses

Usually blind sqli is commnonly used when situations where there is no visible or obvious message responses.

Using conditional payloads such as 1=1 or 1=2, we can observe how application is reacting to these true or false statements. Through indirect response such as page content, response length it is possible to infer if the application is vulnerable,for example in situations like password enumeration.

## Error-Based Blind SQLi

<ins> Error-based SQLi using conditional statements</ins>

Boolean payloads like 1=1 often fail to exploit SQL injection vulnerabilities if they don't induce errors. To overcome this, we can use conditional error-based attacks. By crafting payloads that trigger errors under specific conditions, we can extract sensitive information. 
However, simple boolean payloads may not be sufficient. We need to manipulate the SQL query to introduce conditional logic that can be exploited through error messages.
![image](https://github.com/user-attachments/assets/31e6a936-f68a-4a8d-9951-f87e535480c0)
![image](https://github.com/user-attachments/assets/cbd00e7c-5d10-46ce-bb77-6cc65c5a8905)

<ins>Making use of CASE function</ins>

Using CASE function enables us to force errors based on a certain condition that is specified.
![image](https://github.com/user-attachments/assets/2edf82d3-5982-422e-8111-3f5fa178e269)

**Payload syntax will differ based on the type of database that is used by the application**

In this context we are exploiting oracle db and the TO_CHAR(1/0) function will result in an error since it is impossible to divide 1 by 0 when we inject a true condition using below  query

```sql
SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE 'a' END FROM dual='a'--
```

From here, we can iterate our queries to do something such as passsword enumeration using functions such as substring and length.

```sql
SELECT CASE WHEN (substring(password,1,1)='a') THEN TO_CHAR(1/0) ELSE 'a' END FROM users WHERE username='administrator'='a'--
```


<ins>Visible error-based SQL injection</ins>

We may be able to extract information based on verbose database error messages due to misconfigurations on the database. By doing this, a blind sqli may become a visible one.

```
Unterminated string literal started at position 52 in SQL SELECT * FROM tracking WHERE id = '''. Expected char
```

![image](https://github.com/user-attachments/assets/12e1048e-16ce-4903-a4a2-d3f11c770d56)
