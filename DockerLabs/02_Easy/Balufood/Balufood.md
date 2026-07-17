
---
## **Summary**

Página web de un restaurante con data leakage y escalada de privilegios en linux con user pivoting.

---

![](IMAGES/Balufood%20-%20IMG_01.png)
We start with a quick NMAP scan in order to enumerate open ports. At first, we see 22/SSH and 5000/upnp.

By doing a deeper NMAP scan, we discover that the port 5000 corresponds to a web page (Werkzeug httpd 2.2.2).

So far, the target machine has SSH and a web page exposed.

![](IMAGES/Balufood%20-%20IMG_02.png)

The web page seems to be a restaurant one. The source code does not have anything interesting.

![](IMAGES/Balufood%20-%20IMG_03.png)

On the menu there's 5 sections of the same pizza we "can" order, but also a button to an admin page, let's take a quick look.

![](IMAGES/Balufood%20-%20IMG_04.png)

We try if it's suitable for an SQLi attack trying a single **'** to see if there's an SQL syntax error but it does not seem the case.

![](IMAGES/Balufood%20-%20IMG_05.png)

We then try common combinations like **admin:admin**.

![](IMAGES/Balufood%20-%20IMG_06.png)

Which give us access to the admin page.

On here, we see the orders placed and we may mark them to put them under "In process".

![](IMAGES/Balufood%20-%20IMG_07.png)

Exploring the source code of the admin page, we see backup access credentials, **sysadmin:backup123**.

![](IMAGES/Balufood%20-%20IMG_08.png)

We try those credentials on the SSH and we gain access.

![](IMAGES/Balufood%20-%20IMG_09.png)

There seems that sudo is not installed on the system, also, there isn't any SUID binaries of interest.

![](IMAGES/Balufood%20-%20IMG_10.png)

There seems to be another user on the system, **balulero**.


![](IMAGES/Balufood%20-%20IMG_11.png)

On the /home directory of our current users, there's a file named "app.py". By taking a glance at it, we found what may be a password under the name "app.secret.key = 'cuidaditocuidadin'".

![](IMAGES/Balufood%20-%20IMG_12.png)

We try to use it as the password of the other user account (balulero) and we gain access.

![](IMAGES/Balufood%20-%20IMG_13.png)

By reading the hidden file **.bashrc** on the home of balulero, we find what seems the password of the root user.
We try it and we now have root access to the target machine.

---