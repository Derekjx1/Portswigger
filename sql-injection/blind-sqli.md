---
description: >-
  Blind SQLi is a type of SQL injection vulnerability where application doesn't
  return http response that contain visible result in the injected SQL query.
---

# Blind SQLI

## Testing for Blind SQLI using conditional responses

Using conditional payloads such as 1=1 or 1=2, we can observe how application is reacting to these true or false statements. Through indirect response such as page content, response length it is possible to infer if the application is vulnerable.



