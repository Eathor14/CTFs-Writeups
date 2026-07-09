
---
## **Summary**

Enumeración de directorios web, bases de datos y escalada de privilegios en linux.

---

![](IMAGES/Grooti%20-%20IMG_01.png)

We start with a simple NMAP scan in order to enumerate all the open ports. Upon discovering the open ports, we then do a deeper NMAP scan to enumerate the version and other info about the services.

![](IMAGES/Grooti%20-%20IMG_02.png)
We visit the web page. It seems a pretty simple web with a few links, let's visit them all.

![](IMAGES/Grooti%20-%20IMG_03.png)
On the first link we discover a Directory Listing instead of a tradicional .html. We have 2 files, **README.txt** and *grooti.jpg*. Inside the first one we discover a password (**password1**), now we have to know were it belongs.

The second link drops a 404.

![](IMAGES/Grooti%20-%20IMG_04.png)

And the third one seems to be a invoice.

We have no more internal links to follow so let's inspect the source code.

![](IMAGES/Grooti%20-%20IMG_05.png)

Inspecting the source code we find a pretty interesting message. **Creo que Rocket ha entrado a mi base de datos...**, Rocket might be a username.

![](IMAGES/Grooti%20-%20IMG_06.png)

We fuzz the website in order to discover hidden directories/files and we do discover one, **secret**.

![](IMAGES/Grooti%20-%20IMG_07.png)

Navigating to */secret*, we found 3 usernames: **grooti**, **rocket**, **Naia**, and a link to download a file.

![](IMAGES/Grooti%20-%20IMG_08.png)
![](IMAGES/Grooti%20-%20IMG_09.png)

The file downloaded seems to have a command to connect to the mysql database.

![](IMAGES/Grooti%20-%20IMG_10.png)

We put the command and try the password we have found earlier, we now have access to the mysql database.

![](IMAGES/Grooti%20-%20IMG_11.png)

Exploring the content of the database **files_secret**, we found a promising URL. So we will check it.

![](IMAGES/Grooti%20-%20IMG_13.png)
Analysing the content of this new URL, we have what seems to be a random generator of .txt files depending of the number input.

![](IMAGES/Grooti%20-%20IMG_12.png)

If we take a closer look to the HTTP petition we are making, we see that every time we click on "Transmitir a Grooti" we make a POST petition to a php file called *generate.php*. Also, it seems only the numbers from 1-100 are accepted.

So we will create a easy script with python in order to do the 100 request.

![](IMAGES/Grooti%20-%20IMG_14.png)

After executing the 100 request, we see that the file generated after putting the number "16" seems to be different. It not only has more bytes of information but it also is the only .zip among the list.

![](IMAGES/Grooti%20-%20IMG_15.png)

Now that we have the zip file on our system, we try to unzip it but it ask for a password. We try the previous one "password1" and we obtain a list of what seems to be passwords.

![](IMAGES/Grooti%20-%20IMG_16.png)

We perform a bruteforce on the SSH server vía Hydra and we find valid credentials.

We check if the user is part of the SUDO group, as well as SUID binaries but we find nothing.

![](IMAGES/Grooti%20-%20IMG_17.png)

Then, we check the crontab for any wildcard, we found there's a script executing every time.

![](IMAGES/Grooti%20-%20IMG_18.png)
Inside the */opt/cleanup.sh* there's a call for other script, we check it too.

![](IMAGES/Grooti%20-%20IMG_19.png)
And it seems to be writable for us.

![](IMAGES/Grooti%20-%20IMG_20.png)
![](IMAGES/Grooti%20-%20IMG_21.png)

We then edit the file and create a reverse shell. As the file's owner is root, and the script is running every minute, we will gain access to a root shell.

---