
---
## **Summary**

Fuzzing web sencillo y vulnerabilidad file upload.

---

![](IMAGES/Galería%20-%20IMG_01.png)

We start with a quick NMAP scan in order to identify open ports.
Then, we procede to do a deeper NMAP scan including NSE scripts to enumerate further information about the services running on the ports.

So far, we know the open ports are 21/FTP and 80/HTTP, furthermore, the FTP has the anonymous login enabled.

![](IMAGES/Galería%20-%20IMG_02.png)

Looking at the web page, it seems like an art gallery with no interaction from the user.

![](IMAGES/Galería%20-%20IMG_04.png)
![](IMAGES/Galería%20-%20IMG_03.png)

Either via fuzzing or looking at the web source code, we discover a hidden directory called *gallery*.

![](IMAGES/Galería%20-%20IMG_05.png)
![](IMAGES/Galería%20-%20IMG_06.png)
![](IMAGES/Galería%20-%20IMG_07.png)

It seems we can upload files within the page.

![](IMAGES/Galería%20-%20IMG_08.png)
![](IMAGES/Galería%20-%20IMG_09.png)


![](IMAGES/Galería%20-%20IMG_10.png)

We upload the reverse shell (the one used was provided by pentestmonkey).

![](IMAGES/Galería%20-%20IMG_11.png)

We execute it.

![](IMAGES/Galería%20-%20IMG_12.png)

And now we have access to the target machine.

![](IMAGES/Galería%20-%20IMG_13.png)

We first enumerate the sudo privilege, it seems we can run */bin/nano* as **gallery** without password.

![](IMAGES/Galería%20-%20IMG_14.png)
![](IMAGES/Galería%20-%20IMG_15.png)
![](IMAGES/Galería%20-%20IMG_16.png)

We execute a simply command line (provided by GTFObins) on the *nano* binary as the user **gallery** and now we have access to a normal user account.

![](IMAGES/Galería%20-%20IMG_17.png)

As the user *gallery*, we see it's sudo privilege and we notice we may run */usr/local/bin/runme* as root without a password.

![](IMAGES/Galería%20-%20IMG_18.png)

Analysing the executable with the strings tools, we see it seems to be an image converter.

![](IMAGES/Galería%20-%20IMG_19.png)

If we try to run it, an error pop up: **sh: 1: convert: not found**.

![](IMAGES/Galería%20-%20IMG_20.png)

We create a false convert on the /tmp directory which will spawn an interactive root session

![](IMAGES/Galería%20-%20IMG_21.png)

We make the */tmp* directory as the first one on the PATH, hijacking it.

![](IMAGES/Galería%20-%20IMG_22.png)

We execute the binary and we now have root access.

---