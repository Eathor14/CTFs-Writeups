
---
## **Summary**

Mediante el archivo Docker Compose, se despliegan **10 contenedores** que simulan diferentes segmentos de red. El objetivo es ir comprometiendo cada uno de ellos y pivoteando a través de la infraestructura.

---

https://franc205.notion.site/ESPA-OL-From-Network-to-Network-Hands-On-Pivoting-Techniques-in-Internal-Environments-13b1f42ae1de80cd8c20f0e5a8d29a08


![](IMAGES/Damn%20Vulnerable%20Pivoting%20Enviroment%20-%20IMG_01.png)
![](IMAGES/Damn%20Vulnerable%20Pivoting%20Enviroment%20-%20IMG_02.png)

Comenzamos conectándonos a la máquina **Jerry-PC** con las credenciales SSH filtradas (proporcionadas por el walkthrough), y obtenemos acceso a la primera máquina de la red.

![](IMAGES/Damn%20Vulnerable%20Pivoting%20Enviroment%20-%20IMG_03.png)

Desde la máquina **Jerry**, vemos que tiene dos interfaces de red, por la que hemos entrado y a la que iremos después.

El diagrama de red de momento se ve así:

![[EXCALIDRAW/DiagramaRed - 01.excalidraw]]

![](IMAGES/Damn%20Vulnerable%20Pivoting%20Enviroment%20-%20IMG_04.png)

Hacemos un ping sweep a la nueva red y descubrimos que tiene 3 hosts activos.

![](IMAGES/Damn%20Vulnerable%20Pivoting%20Enviroment%20-%20IMG_05.png)

Vamos a realizar un reenvío de puertos dinámico.

![](IMAGES/Damn%20Vulnerable%20Pivoting%20Enviroment%20-%20IMG_06.png)

Editamos el archivo */etc/proxychains.conf* en la máquina atacante y comentamos la línea de *proxy_dns* y añadimos *socks5 127.0.0.1 1080*.

![](IMAGES/Damn%20Vulnerable%20Pivoting%20Enviroment%20-%20IMG_07.png)
![](IMAGES/Damn%20Vulnerable%20Pivoting%20Enviroment%20-%20IMG_08.png)
![](IMAGES/Damn%20Vulnerable%20Pivoting%20Enviroment%20-%20IMG_09.png)

Nos conectamos al **attacker-pc** para poder realizar desde ahí un escaneo de puertos.
De esta forma descubrimos los puertos abiertos de la máquina de IP **192.168.11.3**, la cual antes era inaccesible.

![](IMAGES/Damn%20Vulnerable%20Pivoting%20Enviroment%20-%20IMG_10.png)

Realizamos fuzzing a la web y encontramos un directorio llamado **admin**.

![](IMAGES/Damn%20Vulnerable%20Pivoting%20Enviroment%20-%20IMG_11.png)

Nos encontramos con un portal algo extraño.

![](IMAGES/Damn%20Vulnerable%20Pivoting%20Enviroment%20-%20IMG_12.png)

Buscando lo que nos sale en GitHub, damos con la herramienta usada. Ahora que sabemos su nombre vamos a buscarla en Exploit-DB.

![](IMAGES/Damn%20Vulnerable%20Pivoting%20Enviroment%20-%20IMG_13.png)

Analizando el código del exploit **EDB-ID: 51717** , encontramos que pone **1234567890 & {revshell} &;** en el campo de "numara".

![](IMAGES/Damn%20Vulnerable%20Pivoting%20Enviroment%20-%20IMG_14.png)
Reenviamos el puerto 4041 de *Jerry-PC* al 4040 de *Attacker*, dónde levantaremos un escucha de netcat.

![[EXCALIDRAW/DiagramaRed - 02.excalidraw]]