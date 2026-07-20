
---
## **Summary**

Laboratorio para practicar la vulnerabilidad de inyección SQL para conseguir acceso al sistema y posterior escalada de privilegios en Linux.

---

![](IMAGES/Injection%20-%20IMG_01.png)

We start with a quick NMAP scan in order to detect the open ports, then, we execute a deeper NMAP scan to enumerate the services versions.

We know so far that the ports 22/SSH and 80/HTTP are both open.

![](IMAGES/Injection%20-%20IMG_02.png)

Fuzzing it doesn't give us any information.

![](IMAGES/Injection%20-%20IMG_03.png)

The web page present us a login page. We will test SQLi.

![](IMAGES/Injection%20-%20IMG_04.png)

By typing a single **'**, the an SQL error appears, which means the page is vulnerable to an SQLi attack.

![](IMAGES/Injection%20-%20IMG_05.png)

We try with a simple SQLi syntax (**admin' OR 1=1 -- -**).

![](IMAGES/Injection%20-%20IMG_06.png)

And we've gain access.

This page tell us 2 valuable things:
1. Dylan as a username.
2. KJSDFG789FGSDF78 as a password/hash.

![](IMAGES/Injection%20-%20IMG_07.png)

We try to access to the SSH as the user **dylan** and the password **KJSDFG789FGSDF78** and we gain access.

![](IMAGES/Injection%20-%20IMG_08.png)

The target machine doesn't have sudo installed, but we have the SUID activated on the */usr/bin/env* binary.

![](IMAGES/Injection%20-%20IMG_09.png)

We exploit the fact that */usr/bin/env* has the SUID enabled by spawning an sh terminal as it's parameter, giving us root privileges over the target machine.

---