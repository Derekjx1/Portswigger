#  SQL injection with filter bypass via XML encoding
Usually SQL injection seem to be in the URI query parameters but as long as there are input sources that interacts with a database and have insufficient input validations can be an attack surface for SQLi
![image](https://github.com/user-attachments/assets/3e20d8cd-bffe-4eff-89d2-3663317d1e35)





Using tools like hackvector we can easily manipulate payloads such as xml encoding to bypass certain defenses such as WAF


# Second Order SQLi

Second order SQL injection is when attacker sends a http request with a malicious payload that is stored and later using it to exploit the vulnerability such as in a database.
This is also known as stored SQL injection where victims unintentionally activates the payload when the http request retrieves data from the database. 
