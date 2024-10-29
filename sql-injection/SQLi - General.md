# SQL Injection

What is SQLi?

SQL Injection (SQLi) is a code injection technique that hackers use to attack data-driven applications by inserting malicious SQL statements into an entry field for execution, potentially allowing them to retrieve, modify, or delete database data and gain unauthorized access

## Testing for SQLi

* Single quote character ' - SQL syntax terminates string in the parameter value and would likely cause an error
* Boolean expressions such as 1=1 or 1=2 to inject true or false statements to see how application reacts
* Time delays (sleep function etc. ) to look for time differences for app to respond
* OAST payloads  that can trigger out of  band interactions with app


## Retrieve hidden data in a web app

In this example, we are able to inject the 1=1 clause into the SQL statements to get the application to retrieve all the products regardless of its product cateory. This is possible because the SQL statement at the backend will always evaluate to true. A

At the backend this statement will most likely be translated to `SELECT * FROM PRODUCTS WHERE CATEGORY=''OR 1=1--`

'  - closes the string and -- comments out the rest of the SQL statement after the injected code

<figure><img src="../.gitbook/assets/image (87).png" alt=""><figcaption><p>From SQLi labs in portswigger</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (88).png" alt=""><figcaption><p>App UI shows all the products</p></figcaption></figure>

## Subverting application logic  - Login bypass

If an application includes login functionality, it likely uses a backend database to store and verify user credentials, such as usernames and passwords. For example, it may execute a query like&#x20;

```sql
SELECT * FROM users WHERE username = 'wiener' AND password = 'bluecheese';
```

Hackers can exploit login logic by terminating the username string and commenting out the rest of the query. For instance, an attacker can bypass authentication for the administrator role without knowing the password using:

<pre class="language-sql"><code class="lang-sql"><strong>SELECT * FROM users WHERE username = 'administrator'-- AND password = ''
</strong></code></pre>



<figure><img src="../.gitbook/assets/image (89).png" alt=""><figcaption></figcaption></figure>

## Union Attacks

Pre-requsites for union attack to work

1. Queries to both tables need to yield the same number of column
2. Data type for each column need to be same

Determining the number of columns in DB using ORDER BY and UNION SELECT methods

Using ORDER BY method determines the number of columns in a query. The goal is to keep increasing the column number until an error is thrown

```sql
ORDER BY 1 -- No error
ORDER BY 2 -- No error
ORDER BY 3 -- Error; Table query only contains 2 columns
```

Using the UNION SELECT method involves querying payloads with varying number of null columns. Because for our query to work, data type would need to be compatible and NULL data type can be converted to most data types.&#x20;

```sql
UNION SELECT NULL, NULL, NULL --
```

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

From here, depending on how the application's is displaying data, it is possible to display and get sensitive data such as querying the database version

```sql
UNION SELECT NULL, @@version, NULL --
```

We can input arbitary data type such as string value to test what data type the column  is set as&#x20;

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption><p>Determining datatype compatibility</p></figcaption></figure>

Retrieve Data from other tables

Using UNION SELECT methods, we can potentially query the database and retrieve sensitive information from other table such as usernames and passwords

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption><p>Making use of the concat comman, we can retrieve username and passwords  from DB</p></figcaption></figure>



## Important points in SQLi

1. Finding out database type & version
2. Tables and table schema

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption><p>Testing using MYSQL syntax and querying version </p></figcaption></figure>

Listing Database contents

Using information\_schema.tables for the postgres db  syntax, we can extract useful database information like user details and passwords.

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption><p>Excluding pg_catalog and information_schema as these are metadata about the database that does not usually contain application data which we are most likely interested in</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption><p>Extracting usernames and passwords</p></figcaption></figure>
