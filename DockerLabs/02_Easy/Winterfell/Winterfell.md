
---
## **Summary**

Laboratorio para practicar enumeración web y SMB, con escalada de privilegios mediante movimiento entre usuarios.

---

![](IMAGES/Winterfell%20-%20IMG_01.png)

We start with a NMAP scan in order to discover open ports and services.

It seems like the ports 22/SSH, 80/HTTP, 445/SMB are open.

![](IMAGES/Winterfell%20-%20IMG_02.png)

The web page seems pretty simple. The navbar does nothing and the button "Reproducir sonido" just plays the opening theme of GoT. The names that appear *jon*, *arya*, *daenerys* might be possible system usernames.

![](IMAGES/Winterfell%20-%20IMG_03.png)

The source code doesn't have much, apart from the script that plays the music, the rest seems pretty simple.

![](IMAGES/Winterfell%20-%20IMG_04.png)

Fuzzing the web, we find the resource */dragon*, let's take a look at it.

![](IMAGES/Winterfell%20-%20IMG_05.png)
![](IMAGES/Winterfell%20-%20IMG_06.png)

*/dragon* only contains one file named "EpisodiosT1", which contains a list of the title names of the 10 episodes of the first season of GoT. This names might be credentials

![](IMAGES/Winterfell%20-%20IMG_07.png)

We enumerate shared files of  SMB and enumerate the local user **jon**.

![](IMAGES/Winterfell%20-%20IMG_08.png)

We try to login to the sharefile *shared* as the user *jon* but it ask us a password, we try the very first one of the titlenames we found earlier and we got in. Inside the share, there's a file named "proteccion_del_reino" and we download it  to our local machine.

![](IMAGES/Winterfell%20-%20IMG_09.png)
![](IMAGES/Winterfell%20-%20IMG_10.png)

The message tell us a password, but it is encrypted. The equal sign at the ends tell us the encryption method used was BASE64. So we decrypt it and now have the possible password "hijodelanister". Let's try it on the SSH.

![](IMAGES/Winterfell%20-%20IMG_11.png)

We now have gained access to the target as the user jon.

![](IMAGES/Winterfell%20-%20IMG_12.png)

It seems like we have a python script on our /home directory. The file's owner is aria.

![](IMAGES/Winterfell%20-%20IMG_13.png)

The script seems to import two libraries *hashlib* and *getpass*. We will try to kindnap the importation of hashlib by crating a malicious copy.

![](IMAGES/Winterfell%20-%20IMG_14.png)
![](IMAGES/Winterfell%20-%20IMG_15.png)

The user aria may run python3 on the script without the use of a password, so we can do lateral movement to it.

![](IMAGES/Winterfell%20-%20IMG_16.png)

On the user aria, we see that another user "daenerys" may run without a password *cat* and *ls*.

![](IMAGES/Winterfell%20-%20IMG_17.png)

Ad daenerys may run *ls* and *cat*, we list the files of her home directory, and then we see the content of the only file that we found. The file in question has the password for the user **daenerys**.

![](IMAGES/Winterfell%20-%20IMG_18.png)

Now that we have access to the user daenerys, we see that there's a script that we can run as root without password.

![](IMAGES/Winterfell%20-%20IMG_19.png)

The script seems to be a reverse shell, but daenerys is the owner and we can modify it.

![](IMAGES/Winterfell%20-%20IMG_20.png)
![](IMAGES/Winterfell%20-%20IMG_21.png)

We execute the new script and we gain access as the root user.


