
---
## **Summary**

Laboratorio para practicar el análisis del código fuente, fuerza bruta y escalada de privilegios mediante movimiento entre usuarios.

---

![](IMAGES/Paradise%20-%20IMG_01.png)

We start with a simple NMAP scan in order to see the open ports, we discover that ports 22/SSH, 80/HTTP, 139/SMB and 445/SMB are open.

Then, we procede with a deeper NMAP scan, including NSE scripts to discover further information.

![](IMAGES/Paradise%20-%20IMG_02.png)

The web page seems pretty simple.

The main source code doesn't have anything of importance.

![](IMAGES/Paradise%20-%20IMG_03.png)

If we click on *Go to the paradise* we will be received by a gallery of photos.

![](IMAGES/Paradise%20-%20IMG_04.png)

And inspecting it's source code, we find what seem to be an encrypted message, judging by the two final equal signs, it seems to be encoded in BASE64.

![](IMAGES/Paradise%20-%20IMG_05.png)

And we decode it easily, the message was **estoesunsecreto**, it might be a hidden directory within the web.

![](IMAGES/Paradise%20-%20IMG_06.png)

Navigating to **/estoesunsecreto/**, we found what seems a message.

![](IMAGES/Paradise%20-%20IMG_07.png)

The user "lucas" is vulnerable to brute force.

![](IMAGES/Paradise%20-%20IMG_08.png)

If we click on the *Book your accommodation*, we will be move to a page which ask us for an email in order to get an access code.

It seems no matter what email we put, we always get a "fail" message.

![](IMAGES/Paradise%20-%20IMG_09.png)

Fuzzing the web we discover a hidden directory **/img/** and a **login.php**.

![](IMAGES/Paradise%20-%20IMG_10.png)

The **/img/** directory seem to have the images used on the gallery, but the are 2 image 6, one labelled as the "original" one.

![](IMAGES/Paradise%20-%20IMG_11.png)

On the login page, we need an email and an access code. We can discard SQLi.

We can't do anything more with the web server, let's take a look at SMB.

![](IMAGES/Paradise%20-%20IMG_12.png)

Using **enum4linux**, we discover 2 local users, *andy* and *lucas* which we knew from earlier.

Now that we have confirmed the username **lucas** withing the target, we will try to bruteforce our way in using hydra on the SSH.

![](IMAGES/Paradise%20-%20IMG_13.png)

And we discover that lucas has the password **chocolate**.

![](IMAGES/Paradise%20-%20IMG_14.png)

And we have gained internal access.

![](IMAGES/Paradise%20-%20IMG_15.png)

We can run the binary **/bin/sed** as the other user *andy* without a password.

![](IMAGES/Paradise%20-%20IMG_16.png)

Using the facilities granted by GTFObins, we successfully did lateral movement to andy.

![](IMAGES/Paradise%20-%20IMG_17.png)

As the user andy, if we try to see it's sudo privilege we need the password. So, we look for binaries with the SUID activated. We found two located under /usr/local/bin, **privileged_exec**, and **backup.sh**.

![](IMAGES/Paradise%20-%20IMG_18.png)

We see that privileged_exec spawns a /bin/bash session.

![](IMAGES/Paradise%20-%20IMG_19.png)

By simply executing the binary we gain root access.

---