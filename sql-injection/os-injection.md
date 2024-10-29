# OS Injection

## Code injection vs OS command injection

| Feature                   | Code Injection                                                     | OS Command Injection                                                |
| ------------------------- | ------------------------------------------------------------------ | ------------------------------------------------------------------- |
| **Type of Code Injected** | Programming language code (e.g., SQL, PHP)                         | Operating system commands                                           |
| **Execution Environment** | Application runtime environment                                    | Operating system shell                                              |
| **Impact**                | Execute malicious code within the application                      | Execute arbitrary commands on the underlying OS                     |
| **Example**               | Injecting SQL code into a search bar to steal data from a database | Tricking a web form into deleting a file by injecting a `rm`command |

Code injection typically targets vulnerabilities within an application, constrained by the application's features like its programming language and libraries. In contrast, OS command injection exploits the underlying system shell on mostly any programming language and platform

## Injecting shell command in payload

<figure><img src=".gitbook/assets/image (86).png" alt=""><figcaption><p>Injection of  OS command injection in the body</p></figcaption></figure>

Injected command in the storeId was `whoami`

* &  -  terminates the current query
* whoami - command that was injected

& character is a shell command separator used for OS commands. This is can be used to chain multiple commands,executed in a sequence at the shell level





Useful OS commands

| Purpose of command    | Linux         | Windows         |
| --------------------- | ------------- | --------------- |
| Name of current user  | `whoami`      | `whoami`        |
| Operating system      | `uname -a`    | `ver`           |
| Network configuration | `ifconfig`    | `ipconfig /all` |
| Network connections   | `netstat -an` | `netstat -an`   |
| Running processes     | `ps -ef`      | `tasklist`      |
