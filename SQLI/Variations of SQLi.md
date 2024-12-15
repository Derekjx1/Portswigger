<h1>SQL injection with filter bypass via XML encoding</h1>
<p>Usually SQL injection seem to be in the URI query parameters but as long as there are input sources that interacts with a database and have insufficient input validations can be an attack surface for SQLi.</p>
![image](https://github.com/user-attachments/assets/2a7cf7ff-681f-43f0-b195-dc7163eb63b5)
<p>Using tools like hackvector we can easily manipulate payloads such as xml encoding to bypass certain defenses such as WAF.</p>
