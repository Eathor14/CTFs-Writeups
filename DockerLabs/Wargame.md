
---
## **Summary**

El objetivo principal de este laboratorio fue practicar técnicas de reconocimiento, análisis de servicios y escalada de privilegios en un entorno controlado con un fuerte componente de lógica y análisis estático.

---

![[Wargame - nmap1.png]]
We start with a fast nmap scan to discover open ports.

![[Wargame - nmap2.png]]
Then, we continue with a deeper script and version scan to enumerate more info about the open ports.

Ports discovered:

| **PORT** | **VERSION**         |
| -------- | ------------------- |
| 21/FTP   | vsftpd 3.0.5        |
| 22/SSH   | OpenSSH 10.0p2      |
| 80/HTTP  | Apache httpd 2.4.65 |

![[Wargame - webView.png]]
If we take a quick peek to the web that's hosted, we see we can't gather much info here.

![[Wargame - gobuster.png]]
We run a gobuster