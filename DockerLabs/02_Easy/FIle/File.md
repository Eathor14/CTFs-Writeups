
---
## **Summary**

Laboratorio para practicar el acceso por FTP anónimo, cracking de hashes y escalada de privilegios mediante un binario SUID.

---

![](IMAGES/File%20-%20IMG_01.png)

We start with a quick NMAP scan to look for open ports, we see the ports 21/FTP and 80/HTTP are open.

Then, we procede with a deeper NMAP scan focused into those two ports in order to enumerate further information.

And it looks like the FTP has the anon login enabled.

![](IMAGES/File%20-%20IMG_02.png)

The web page is the standard APACHE2 page, so it may seem we can't continue over here.

![](IMAGES/File%20-%20IMG_03.png)

But taking a look at the source code we may see a curious message:

- *El que hizo la web no quiso trabajar mucho, por eso hizo un directorio raro ;D*

Let's try fuzzing in order to discover that hidden directory.

![](IMAGES/File%20-%20IMG_04.png)
![](IMAGES/File%20-%20IMG_05.png)

Indeed, there's a hidden directory called **uploads**, and a file called **file_upload.php**. This may be a potential vector of entry.

But first, let's take a look at the FTP

![](IMAGES/File%20-%20IMG_06.png)

Upon entering the FTP we may see a .txt file, let's see what it says.

Apart from the file, it doesn't seem there's much to see here, we can discard the FTP and HTTP sharing the same directory (at least, while logged as anon).

![](IMAGES/File%20-%20IMG_07.png)

It looks like a hash.

![](IMAGES/File%20-%20IMG_08.png)

And it is a MD5 hash

![](IMAGES/File%20-%20IMG_09.png)

Using john, we crack the hash and the true word is **justin**. It may be both a username or a password, we can't tell.

Now, let's take a look at that .php file we saw earlier.

![](IMAGES/File%20-%20IMG_10.png)

We try to upload a php reverse shell (provided by pentestmonkey).

![](IMAGES/File%20-%20IMG_11.png)

And it seems we are getting blocked. Let's try and change a bit the extension.

![](IMAGES/File%20-%20IMG_12.png)
![](IMAGES/File%20-%20IMG_13.png)

By changing the name, we upload it successfully into the web server.

![](IMAGES/File%20-%20IMG_14.png)

By setting up a netcat listener and executing the webshell file in the /uploads directory, we gain a reverse shell and now we have access into the target machine.

![](IMAGES/File%20-%20IMG_15.png)
![](IMAGES/File%20-%20IMG_16.png)

It seems like there're 4 other users on the system, but neither of them has the password **justin**

![](IMAGES/File%20-%20IMG_17.png)

We download into the target:
- https://github.com/Maalfer/Sudo_BruteForce/blob/main/Linux-Su-Force.sh
- rockyou.txt

In order to perform local bruteforce.

![](IMAGES/File%20-%20IMG_18.png)

We execute the script and gain the password.

![](IMAGES/File%20-%20IMG_19.png)

Now, as the user **fernando**, we see that it can't run sudo and has an image on it's homepage.

![](IMAGES/File%20-%20IMG_20.png)

By setting up a quick python server, we can download the image on out local machine.

By trying to extract hidden data within it with steghide, we need a pass code we don't know.
So we use stegseek in order to bruteforce it.

![](IMAGES/File%20-%20IMG_21.png)

Now we have another hash to crack.

![](IMAGES/File%20-%20IMG_22.png)

With *hash-identifier* we see it's a **SHA-1** hash.

![](IMAGES/File%20-%20IMG_23.png)

And now we have the password.

So far, we have two unused passwords:
- justin.
- password123.

![](IMAGES/File%20-%20IMG_24.png)

By trying the last one on the others users, we found is the password for mario.

![](IMAGES/File%20-%20IMG_25.png)

The user mario does not have anything useful on it's homepage, but it can run **/usr/bin/awk** as *julen* without a password.

![](IMAGES/File%20-%20IMG_26.png)

Helping us with https://gtfobins.org/gtfobins/awk/, we exploit the fact that we could run *awk*as julen without a password and we successfully did lateral movement to julen's account.

![](IMAGES/File%20-%20IMG_27.png)

As julen, we see we may run **/usr/bin/env** as the other user *iker* without a password.

![](IMAGES/File%20-%20IMG_28.png)

Now that we have access to iker's user, we see we may run **/usr/bin/python3 /home/iker/geo_ip.py** as root without a password.

![](IMAGES/File%20-%20IMG_29.png)

Glancing at the python code we may run as root without password, we see it import a library. Let's hijack it in order to spawn a bash session and gain root access.

![](IMAGES/File%20-%20IMG_30.png)
![](IMAGES/File%20-%20IMG_31.png)

Executing the python script with the new *request.py* make us root.

![](IMAGES/File%20-%20IMG_32.png)

Also, in the /root directory there's a .txt file with a MD5 hash, by cracking it we see the message **pwned!**

---