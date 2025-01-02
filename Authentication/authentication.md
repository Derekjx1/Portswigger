# Authentication

## AuthN v AuthZ

Authentication is about verifying who a user is claiming to be and authorization is  whether the user is allowed to access and  perform privileged actions

## Brute-forcing

Brute-forcing is a common method to obtain valid user credentials. If an application relies solely on usernames and passwords for authentication, brute-forcing these can lead to account takeovers if there is not enough protection against such attacks. Payloads such as wordlists are readily available&#x20;

### Username Enumeration

Username enumeration occurs when an application reveals valid usernames through its responses to authentication attempts. Check for reponses what a valid username with a wrong password returns as compared to an invalid username & password. Look out for such subtle indications
To prevent this, standardize error messages and responses to avoid disclosing the validity of usernames.

Responses can be studied to give clues to which usernames are valid during enumeration. It can be seeing different status codes, different response contents, different response timing etc.



### 2FA Bypass

Two-factor authentication can be bypassed if the application lacks checks on the logical steps that confirm user login status. Sometimes, when users are prompted for the second factor while already logged in, the application might treat them as authenticated. This loophole allows skipping the second check and directly accessing authenticated pages.
