
---
## **Summary**

El objetivo principal de este laboratorio fue practicar técnicas de reconocimiento, análisis de servicios y escalada de privilegios en un entorno controlado con un fuerte componente de lógica y análisis estático.

---

![nmap](IMAGES/Wargame%20-%20nmap.png)

We start with a fast nmap scan to discover open ports.

![](IMAGES/Wargame%20-%20nmap2.png)

Then, we continue with a deeper script and version scan to enumerate more info about the open ports.

Ports discovered:

| **PORT** | **VERSION**         |
| -------- | ------------------- |
| 21/FTP   | vsftpd 3.0.5        |
| 22/SSH   | OpenSSH 10.0p2      |
| 80/HTTP  | Apache httpd 2.4.65 |

![](IMAGES/Wargame%20-%20webView.png)
If we take a quick peek to the web that's hosted, we see we can't gather much info here.

![](IMAGES/Wargame%20-%20GoBuster.png)
We run gobuster in order to enumerate the hidden and accesible files of the web server. We found one **README.txt**, let's take a look.

![](IMAGES/Wargame%20-%20README.txt.png)
We take a look at the file