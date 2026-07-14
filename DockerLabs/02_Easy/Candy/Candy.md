
---
## **Summary**

Laboratorio para practicar la explotación de un CMS Joomla, accediendo al panel de administración e inyectando una reverse shell.

---

![](IMAGES/Candy%20-%20IMG_01.png)

We start with a simple NMAP scan in order to view de open ports, then, we do a deeper NMAP scan to enumerate the service version and run some NSE scripts.

We discover the target have only the port 80/HTTP open.

![](IMAGES/Candy%20-%20IMG_02.png)

By doing a little fuzzing on the web page we discover there's an administrator panel.

![](IMAGES/Candy%20-%20IMG_03.png)

Also, taking a quick glance at the *robots.txt* we see what looks like possible admin credentials or password.

![](IMAGES/Candy%20-%20IMG_04.png)

Trying to decode it with BASE64 gave us the true password.

![](IMAGES/Candy%20-%20IMG_05.png)
![](IMAGES/Candy%20-%20IMG_06.png)

Now we have access to the administrator panel.

![](IMAGES/Candy%20-%20IMG_07.png)

We add the following line at the template php in order to be able to execute arbitrary commands on the target machine

![](IMAGES/Candy%20-%20IMG_08.png)

Now, we just execute a bash reverse shell code on the URL.

![](IMAGES/Candy%20-%20IMG_09.png)

And now we have gained access to the target machine.

![](IMAGES/Candy%20-%20IMG_10.png)

We can't view sudo privilege as we don't know the user's passwords. We also didn't found any important file which we can modify nor with the SUID available. But we know there's another user on the system.

![](IMAGES/Candy%20-%20IMG_11.png)

We look for others files with the .txt extension as we have one in the root of the web whom owner is root. We found another one in */var/backup/hidden*.

![](IMAGES/Candy%20-%20IMG_12.png)

Looking at the content of the file, we have a little code with the parameters of login to a database.

Earlier, doing a bit of local enumeration we saw other local user named  *luisillo* 

![](IMAGES/Candy%20-%20IMG_13.png)

And now we are logged in as luisillo.

![](IMAGES/Candy%20-%20IMG_14.png)

It seems like luisillo may run the binary */bin/dd* as any user (root) without password.

![](IMAGES/Candy%20-%20IMG_15.png)

We make a copy the */etc/passwd* file.

![](IMAGES/Candy%20-%20IMG_16.png)

We edit it in order to remove the password part (the X) from the root account.

![](IMAGES/Candy%20-%20IMG_17.png)

Then, as we may use */bin/dd* as root without password we overwrite the real */etc/passwd* file with the own of our own, making root has no password thus been available to change to root account with ease.

---