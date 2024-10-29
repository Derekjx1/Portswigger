
# Access Control

We can escalate our privileges to get admin user rights and controls in such situations where access control is misconfigured or not present.

## Vertical Privilege Escalation

### Unprotected privileged Functionality

<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption><p>We can access admin page to delete users  in this instance</p></figcaption></figure>

Some cases where the robot.txt file is available, we are able to access the private file paths that are not normally disclosed and if there are no proper access controls on these private files, they can be exploited.

<figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption><p>Robots.txt</p></figcaption></figure>

### Information disclosure via script in source HTML

We may be able find and leverage information disclosure in HTML source code. Here Javascript code discloses the admin path&#x20;

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption><p>JS script discloses admin functions</p></figcaption></figure>

### Parameter-based access control

Some application may determine user rights at login and store this in user controllable function. Some of these parameters  can be tampered with to gain elevated privilleges

* A hidden field.
* A cookie.
* A preset query string parameter

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption><p>Get privillege access using forged cookie value</p></figcaption></figure>

## Horizontal privilege Escalation

If we can't initially gain super user privileges, we can first access another user with similar rights. This approach can help us find alternative methods for privilege escalation.

Applications with insecure direct object references (IDOR) vulnerabilities can be exploited to access other user accounts. For example, by changing the `id` parameter value to another user's ID.&#x20;

Sometimes applications may use randomized ids which may be unpredictable which make exploiting this more difficult however, we can explore the app to see if such ids are disclosed within the application

<figure><img src="../.gitbook/assets/image (27).png" alt=""><figcaption><p>Disclosure of user ids in the app</p></figcaption></figure>

