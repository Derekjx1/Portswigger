---
description: >-
  Allowing users to upload files without proper input validation can lead to
  serious security risks.
---

# File Upload

File Upload Vulnerabilities&#x20;

Without proper input validation checks, malicious actors can arbitary upload any types and could potentially achieve remote code execution through server-side scripts. When developers fail to restrict file types and even fail to check content of the uploaded file cause this vulnerability to be exploited.

{% hint style="info" %}
In real-world scenarios, simple file upload exploits may fail due to defenses thhat are present to deter uploading of arbitary files. However, insufficient input validation of the file content such as only checking the value of the content-type header value if the server is only expecting certain types of files can still lead to vulnerabilities.
{% endhint %}

## Impacts of file upload vulnerability

### Exploiting unrestricted file uploads to deploy a web shell

Without proper validation of the files that users are able to upload can potentially be an area where malicious actors are able to upload remote shells and achieve remote code execution.&#x20;

Examples would be to upload a php file containing shell code that allows malicous actors to retrieve sensitive data. Upon uploading the file, malicious actors can send a request for this file and backend server will read the content,execute the PHP command and returns it to user

<pre class="language-php"><code class="lang-php"><strong>&#x3C;?php echo file_get_contents('/home/carlos/secret');?>
</strong></code></pre>

<figure><img src=".gitbook/assets/image (85).png" alt=""><figcaption><p>Returning secrets of a user from calling file containing web shell that was uploaded</p></figcaption></figure>

### Overwriting Critical files / Directory Traversal

Permitting certain files names and extensions may allow attacker to overwrite critical files. If directory traversal is permitted, files can be uploaded to directories that are unauthorized for access

### Denial of Service

If files size are not restricted, a form of denial of service may happen where attacker can upload a huge file consuming of the storage  space

### Flawed input file validation

&#x20;Servers may only check for the `content-type` header without actually validating the actual contents in the  file. Such insufficent validations of input files can also still allow malicious users to upload arbitary files.

<figure><img src=".gitbook/assets/image (90).png" alt=""><figcaption><p>Server return 403 ; only allow certain content-type header</p></figcaption></figure>

<figure><img src=".gitbook/assets/image (93).png" alt=""><figcaption><p>Server accepts .php file with malicious payload by changing content-type header</p></figcaption></figure>

<figure><img src=".gitbook/assets/image (94).png" alt=""><figcaption><p>Calling the uploaded file invokes the php code and returns secret</p></figcaption></figure>

### Web shell upload via path traversal

Web servers may also restrict executing arbitary files when files are uploaded in user supplied directories. They may be configured in a way that only executes certain types of files according to the MIME type otherwise they may return contents of the file in plain text or error message. However, If its possible to upload the file into a different directory, usually not readily accessible by normal users then it might be possible for executables to be run.

<figure><img src=".gitbook/assets/image (97).png" alt=""><figcaption><p>Server side serves php file in plain text </p></figcaption></figure>

<figure><img src=".gitbook/assets/image (95).png" alt=""><figcaption><p>Server side allows the uploading of file to different directory using path traversal vulnerability</p></figcaption></figure>

<figure><img src=".gitbook/assets/image (98).png" alt=""><figcaption><p>PHP file gets executed in a different directory with less strict controls</p></figcaption></figure>

### Web shell upload via extension blacklist bypass

Web servers may be configure to blacklist certain file extensions(php,python etc), preventing such files being uploaded and executed by server. However if server is misconfigured in way that allows the uploading of certain configuration files such as apache.conf or .htaccess, we can overwrite the existing configurations to allow the file types we want.

<figure><img src=".gitbook/assets/image (8).png" alt=""><figcaption><p>Web server blocks php files; returns 403 error code</p></figcaption></figure>

<figure><img src=".gitbook/assets/image (9).png" alt=""><figcaption><p>Web server doesn't block .htaccess file extensions, uploading it overwrites existing config. </p></figcaption></figure>

Here .htaccess file is uploaded with a config telling server to treat .p3p extensions as php files. As this is a custom file extension, there's a high chance that server is not blocking it.

<figure><img src=".gitbook/assets/image (10).png" alt=""><figcaption><p>Web shell uploaded succesfully with arbitrary file extension</p></figcaption></figure>

<figure><img src=".gitbook/assets/image (12).png" alt=""><figcaption><p>Web shell executed when called from client</p></figcaption></figure>

### Web shell upload via obfuscated file extension

Some defenses like striping extensions or denying certain file extensions upon detecting keywords(like php,exe etc) can be potentially overcome this by obfuscating file extensions. Using payloads such as including null bytes %00 , using double url encoding , capitalisng letters can help to evade such defenses if they are not set up or configured properly.

<figure><img src=".gitbook/assets/image (13).png" alt=""><figcaption><p>Web server accepts php file with obfuscated  null byte payload</p></figcaption></figure>

<figure><img src=".gitbook/assets/image (14).png" alt=""><figcaption><p>Requesting the file again executes php contents in the file</p></figcaption></figure>

### Remote code execution via polyglot web shell upload

Server may have some protection in verifying contents of the file before permitting the upload. Such as verifying if jpeg files contains the jpeg file signature which is starts with **FF D8 FF** in HEX. However, such protection mechanism is not foolproof as tools such as exiftool can help to alter the metadata of the file



