
---
## **Summary**

Laboratorio para practicar inyección SQL, fuerza bruta SSH y escalada de privilegios en Linux.

---

![](IMAGES/Internship%20-%20nmap.png)

We start with a fast nmap scan to discover open ports. Then, we continue with a deeper script and version scan to enumerate more info about the open ports.

We discover the ports 22/SSH and 80/HTTP are both open.

![](IMAGES/Internship%20-%20web.png)
Let's take a quick look at the web page. At a first glance, this is a simple self-hosted web page.
We have 3 options in the header, *Sobre nosotros*, *Contacto*, *Iniciar Sesión*, but they seem unresponsive for now.

![](IMAGES/Internship%20-%20sourcecode.png)
Inspecting the source code of the web page we discover the domain **gatekeeperhr.com**, so we will add it to our /etc/hosts file.
Doing this we can now access correctly to the web page and view all it content, including the 3 other pages we've seen earlier.

Glancing at the "About Us" page we can't gain any useful information.

![](IMAGES/Internship%20-%20contact.png)

But on the contact page we might have a possible local username: **mariana**.
Let's view the login page now.

![](IMAGES/Internship%20-%20sqli1.png)

Trying the simplest SQLi injection method fails.

![](IMAGES/Internship%20-%20%20sqli2.png)
![](IMAGES/Internship%20-%20sqli3.png)
But modifying the syntax a little bit we gain access to a admin Dashboard, we we see a lot of different names, which might be local usernames.

![](IMAGES/Internship%20-%20gobuster.png)
We use gobuster on the domain to search for hidden directories. We found */spam*, let's take a look.

![](IMAGES/Internship%20-%20sourcecodeSpam.png)
The page */spam* is an all black page, but in it's source code we found a curious message which seems to be codified.

The code seems pretty simple. It maintains the common spaces, the length of the words seems unalterated and it uses special characters *ñ*, punctuation signs *'* , and numbers *3*. 
It seems to be codified in ROT13.

![](IMAGES/Internship%20-%20decodification.png)
We try to decodify following ROT 13 and we find the true message, which tell us that one of the intern's password is **purpl3**

Now, we don't have any more to do at the web page, but the machine also had SSH, let's try and bruteforce the service with the usernames we have found, and the password we have decodified.

![](IMAGES/Internship%20-%20hydra.png)
We discover that the user *pedro* is the one with the password *purpl3*.

![](IMAGES/Internship%20-%20sshLogon.png)

Now we have gained access to the target machine.

We see if pedro is part of the sudo group but it seems not.

![](IMAGES/Internship%20-%20writeableFile.png)

Then, we look for files we can write on and we find one pretty interesting, **/opt/log_cleaner.sh.**

![](IMAGES/Internship%20-%20reverseshell1.png)

We edit the script to generate a reverse shell to our machine and we set up a netcat listener.

![](IMAGES/Internship%20-%20revershell2.png)

By exiting the ssh and re-entering, we launch the script and we gain a reverse shell to Valentina's account.

![](IMAGES/Internship%20-%20profilepicture.png)
![](IMAGES/Internship%20-%20cpprofilepicture.png)

Let's take a look at the .jpeg, so we copy it to /tmp and change it's permissions to 777, then, we copy it to our machine.

![](IMAGES/Internship%20-%20steghide.png)

We then use steghide to extract the secret files that may be inside the image. It ask us for a password, we simply press enter.

Inside the **secret.txt** we have found, we find a word **mag1ck**.

![](IMAGES/Internship%20-%20sshValentina.png)

We try to use it at Valentina's password and we get access. Then, we found that Valentina is indeed, part of the sudo group.

![](IMAGES/Internship%20-%20privesc.png)
![](IMAGES/Internship%20-%20privesc2.png)

We simply try to escalate to superuser and we can without a password.

---