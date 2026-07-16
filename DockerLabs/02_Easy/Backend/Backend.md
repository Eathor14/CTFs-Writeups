
---
## **Summary**

Laboratorio para practicar inyección SQL con sqlmap para volcar credenciales y acceder por SSH.

---

![](IMAGES/Backend%20-%20IMG_01.png)

We start with a quick NMAP scan in order to discover open ports on the target machine.

Then, we do a deeper NMAP scan to enumerate the services version and execute NSE scripts on them.

We view the ports 22/SSH and 80/HTTP are both open.

![](IMAGES/Backend%20-%20IMG_02.png)
![](IMAGES/Backend%20-%20IMG_03.png)

Both the web page and it's source code doesn't seem to have much. There's also a login page, so let's check it.

![](IMAGES/Backend%20-%20IMG_04.png)

The source code is also not very useful.

![](IMAGES/Backend%20-%20IMG_05.png)

If we put a single ' on the login parameters, the internal database will throw us an error, which means the page is suitable por SQLi attacks.

By trying different types of SQLi querys, we do not get access.

![](IMAGES/Backend%20-%20IMG_06.png)
![](IMAGES/Backend%20-%20IMG_07.png)

We then procede to use SQLMAP in order to try and get some credentials.

We find the following:
- paco:$paco$123 
- pepe:P123pepe3456P
- juan:jjuuaann123

Trying to login in the login page just get us back to the home page.

There seems we can't continue through the web page, so let's try these 3 credentials on the SSH.

![](IMAGES/Backend%20-%20IMG_08.png)

Trying the first and third users and credentials don't log us, but the second one (pepe:P123pepe3456P) does.

So now we have access to the target.

![](IMAGES/Backend%20-%20IMG_09.png)

It seems that "pepe" is the only users on the system apart from root, so no lateral movement.

![](IMAGES/Backend%20-%20IMG_10.png)

It also seems that **sudo** is not installed.

![](IMAGES/Backend%20-%20IMG_11.png)

Looking for any useful binary with the SUID enabled we found **ls** and **grep** on the list, which means we can list the content and read the files of root directories, 

![](IMAGES/Backend%20-%20IMG_12.png)

It seems there's a hash file on the root directory, probably it's own password hash. 

![](IMAGES/Backend%20-%20IMG_13.png)

Using **grep** to look for something there's on every file, we get to read the whole file.

We now have what's most probably the hash of the root password.

![](IMAGES/Backend%20-%20IMG_14.png)

Using the web hashes.com we calculate the real password, which seems to be:
- spongebob34

![](IMAGES/Backend%20-%20IMG_15.png)

And we gain root access.

---