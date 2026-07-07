
---
## **Summary**

Laboratorio para practicar el análisis del código fuente para obtener credenciales filtradas y escalar privilegios en Linux.

---

![](IMAGES/Balulero%20-%20IMG_01.png)

We start with a simple nmap scan in order to discover the open ports, then, we continue with a deeper scan to enumerate the versión and more useful information.

It seems like the open ports are 22/SSH and 80/HTTP.

![](IMAGES/Balulero%20-%20IMG_02.png)

The page doesn't seem to have any login page so we can discard SQLi.

![](IMAGES/Balulero%20-%20IMG_03.png)

We try to enumerate and fuzz the page in order to discover hidden files and/or directories but we find nothing.

![](IMAGES/Balulero%20-%20IMG_04.png)

We then start analysing the source code, and we find 2 interesting script calls. Let's take a glance at them.

There is nothing useful inside *imagenes.js*, but there is something useful inside **script.js**

![](IMAGES/Balulero%20-%20IMG_05.png)

Reading the code from **script.js** we find a commentary talking about a hidden file called .env that stores the password, a file that it is said to be hidden, but there is also another visible file called **.env_de_baluchingon**.

![](IMAGES/Balulero%20-%20IMG_06.png)

Inspecting the file mentioned, we have discover credentials, let's try them at the SSH login.

![](IMAGES/Balulero%20-%20IMG_07.png)

We now have access at the local machine.

![](IMAGES/Balulero%20-%20IMG_08.png)

It seems that apart from the root and balu users, there is one more called *chocolate*.

![](IMAGES/Balulero%20-%20IMG_09.png)

We see that we may run php as the user **Chocolate**, so doing it we can do lateral movement to that user account.

![](IMAGES/Balulero%20-%20IMG_10.png)

By enumerating the local process, we find one that is interesting. It seems like root is always executing a process, let's take a look at the script in question.

![](IMAGES/Balulero%20-%20IMG_11.png)
The script owner is the user we are logged in, and we can edit it.

![](IMAGES/Balulero%20-%20IMG_12.png)

We will use a php reverse shell from pentestmonkey. We establish an HTTP server with the python3 utility and replace the **/opt/script.php** file with the one of our own.

![](IMAGES/Balulero%20-%20IMG_13.png)

We then set up a netcat listener and we gain a root shell.

---
