
---
## **Summary**

Laboratorio para practicar el bypass de autenticación e inyección SQL para comprometer la base de datos y escalar privilegios.

---

![](IMAGES/Mirame%20-%20IMG_01.png)

We start doing a quick NMAP scan in order to detect open ports.
Then, we do a deeper scan to enumerate the services version.

So far, we have discovered the open ports 22/SSH and 80/HTTP.

![](IMAGES/Mirame%20-%20IMG_02.png)

Exploring the web page we are received by a login page.

![](IMAGES/Mirame%20-%20IMG_03.png)

The source code seems pretty simple and does not tell us much.

![](IMAGES/Mirame%20-%20IMG_04.png)

Fuzzing the web doesn't tell us much either.

We will try to bypass the login page.

![](IMAGES/Mirame%20-%20IMG_05.png)

By putting as the username **admin' OR 1=1 ##** we successfully do a SQLi attack and bypass the login page.

![](IMAGES/Mirame%20-%20IMG_06.png)
![](IMAGES/Mirame%20-%20IMG_07.png)

This new page seems to be a weather check.

As there's nothing of interest here, and we know the database is susceptible to SQLi, we will use SQLMAP.

![](IMAGES/Mirame%20-%20IMG_08.png)
![](IMAGES/Mirame%20-%20IMG_09.png)

And now we have 4 users with 4 passwords.

We try to use them with hydra on the SSH, but we did not gain access.

The  fourth user is called "directoriotravieso", so we will try and see if it's the name of a hidden web directory.

![](IMAGES/Mirame%20-%20IMG_10.png)
And it seems so.

![](IMAGES/Mirame%20-%20IMG_11.png)

The only thing that is there, is an eyes image.

![](IMAGES/Mirame%20-%20IMG_12.png)

We download the image to further investigation.

![](IMAGES/Mirame%20-%20IMG_13.png)
We first try to see if there's anything hidden within the image, but we are asked for a password. We then use the tool *stegseek* in order to quickly crack the password, it being **chocolate**.

With the correct password we then extract the content of the image, which is a zip file.

![](IMAGES/Mirame%20-%20IMG_14.png)

We are asked a password. We try **chocolate** again but it did not work.

![](IMAGES/Mirame%20-%20IMG_15.png)

We use the tool *fcrackzip* in order to crack the password.

![](IMAGES/Mirame%20-%20IMG_16.png)

We know have what seems to be SSH credentials.

![](IMAGES/Mirame%20-%20IMG_17.png)

We know have access to the target machine. We also check if the user has any sudo privilege, it seems not.

![](IMAGES/Mirame%20-%20IMG_18.png)

We find that the binary **find** has the SUID bit activated.

![](IMAGES/Mirame%20-%20IMG_19.png)

Via the help of [GTFObins](https://gtfobins.org/gtfobins/find/) we gain root access exploiting the fact that find has the SUID bit activated.

---