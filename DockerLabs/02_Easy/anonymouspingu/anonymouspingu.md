
---
## **Summary**

FTP login como anonymous activado y que a su vez está vinculado con el servidor web de la máquina.

---

![](IMAGES/anonymouspingu%20-%20IMG_01.png)

We start with a simple NMAP scan in order to look for open ports, then, to those open ports we perform a second NMAP scan to enumerate the versión and run common NSE scripts.

So long we have found the following open ports:  21/TCP and 80/HTTP.

Regarding the NSE scripts used at the FTP, we know 2 vital things:
1. The FTP has the anonymous logon allowed.
2. The root directory of the FTP seems like a web directory.

![](IMAGES/anonymouspingu%20-%20IMG_02.png)
![](IMAGES/anonymouspingu%20-%20IMG_03.png)
The web page is pretty normal. We take a glance at every single option there is and we see that there's a section called "Acceder al Backend" right on the navbar.

![](IMAGES/anonymouspingu%20-%20IMG_04.png)

By acceding it we see a directory index of */upload*, which is pretty odd the ease of access. We have found this directory earlier while running the NSE scripts.

![](IMAGES/anonymouspingu%20-%20IMG_05.png)

Fuzzing the web page does not give us any additional information. So, let's try and gain access via de FTP and the directory /uploads.

![](IMAGES/anonymouspingu%20-%20IMG_06.png)

We enter the FTP via anon logon.

![](IMAGES/anonymouspingu%20-%20IMG_07.png)
![](IMAGES/anonymouspingu%20-%20IMG_08.png)

We create a PHP reverse shell, the one used here is from PentestMonkey, and upload it unto the target directory.

![](IMAGES/anonymouspingu%20-%20IMG_09.png)

By simply executing the file with a netcat listener set up to the desired local port, we successfully create a reverse shell.

![](IMAGES/anonymouspingu%20-%20IMG_10.png)

First of we see if the user we are logged in (www-data) has sudo privilege, we see we can execute the binary **/usr/bin/man** as the other user *pingu* without password.

![](IMAGES/anonymouspingu%20-%20IMG_11.png)

We execute the man binary as the user pingu and inside it we type **!/bin/bash** shebang, doing so, we do lateral movement to the user pingu.

![](IMAGES/anonymouspingu%20-%20IMG_12.png)

As the user pingu, we look for sudo privilege and we see that we can run the following binaries as another user called *gladys*:
- /usr/bin/nmap.
- /usr/bin/dpkg.

![](IMAGES/anonymouspingu%20-%20IMG_13.png)

We repeated what we've done just before but this time with the *DPKG* package.

![](IMAGES/anonymouspingu%20-%20IMG_14.png)

As *gladys* we see that we can run the *chown* binary as root and without a password.

![](IMAGES/anonymouspingu%20-%20IMG_15.png)

We change the user own and group of the */etc/passwd* file to the current user.

![](IMAGES/anonymouspingu%20-%20IMG_16.png)
Now, we can modify the */etc/passwd* file, so we add a new user with the UID and GID 0 (root ones) and a shell, and by simply changing unto that new user we gain root access to the machine.

---