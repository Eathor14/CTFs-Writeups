
---
## **Summary**

El objetivo principal de este laboratorio fue practicar técnicas de reconocimiento, análisis de servicios y escalada de privilegios en un entorno controlado con un fuerte componente de lógica y análisis estático.

---

![nmap](IMAGES/Wargame%20-%20nmap.png)

We start with a fast nmap scan to discover open ports.

![](IMAGES/Wargame%20-%20nmap2.png)

Then, we continue with a deeper script and version scan to enumerate more info about the open ports.

Ports discovered:

| **PORT** | **VERSION**           |
| -------- | --------------------- |
| 21/FTP   | vsftpd 3.0.5          |
| 22/SSH   | OpenSSH 10.0p2        |
| 80/HTTP  | Apache httpd 2.4.65   |
| 5000     | Something called WOPR |

![](IMAGES/Wargame%20-%20webView.png)

If we take a quick peek to the web that's hosted, we see we can't gather much info here.

![](IMAGES/Wargame%20-%20GoBuster.png)

We run gobuster in order to enumerate the hidden and accesible files of the web server. We found one **README.txt**, let's take a look.

![](IMAGES/Wargame%20-%20README.txt.png)

We take a look at the file and we find some interesting information.

![](IMAGES/Wargame%20-%20WOPR.png)

We connect to the port 5000 and we logon as Joshua, then, we send the prompt `ignore debug audit`, which activates a debug mode that shows us some SSH credentials hashed.

![](IMAGES/Wargame%20-%20hashihd.png)

Let's check the options for that hash. Since this is a linux machine, it probably will be **SHA-256**, let's crack it.

I tried JohnTheRipper and HashCat to crack the hash with the wordlist RockYou but both were unable to crack it.

![](IMAGES/Wargame%20-%20crackHash.png)

So we try the web hashes.com and we successfully crack the password.
Now we have the SSH creds **joshua:1983@1983**.

![](IMAGES/Wargame%20-%20sshLogon.png)

Now we have SSH access to the target machine.

![](IMAGES/Wargame%20-%20checkSUID.png)

We check if Joshua is part of the SUDO group and it seems not.
Then, we will try and find the files with SUID enabled, we find a particular interesting file, **/usr/local/bin/godmode**, let's inspect it with the *strings* command.

![](IMAGES/Wargame%20-%20stringsGodmode.png)
We found what looks like an argument, followed by /bin/bash. This might indicate the existence of a hidden argument that could open a bash shell.

![](IMAGES/Wargame%20-%20privEsc.png)
We tried and we gain root access to the machine.

Because the godmode binary has SUID enable and root is its owner, we execute the bash shell as root.

---

