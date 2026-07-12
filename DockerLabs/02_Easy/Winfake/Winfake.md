
---
## **Summary**

Laboratorio sencillo donde debemos inspeccionar el código fuente para obtener información de cómo realizar la intrusión.

---

![](IMAGES/Winfake%20-%20IMG_01.png)

We start with a NMAP scan in order to discover open ports and it services.
We see we have 22/SSH and 80/HTTP ports open.

![](IMAGES/Winfake%20-%20IMG_02.png)
![](IMAGES/Winfake%20-%20IMG_03.png)
![](IMAGES/Winfake%20-%20IMG_04.png)

Taking a look at the web page, we see it is non-interactive. Let's take a look at its sourcecode.

![](IMAGES/Winfake%20-%20IMG_05.png)
![](IMAGES/Winfake%20-%20IMG_06.png)

![](IMAGES/Winfake%20-%20IMG_07.png)
![](IMAGES/Winfake%20-%20IMG_08.png)

Reviewing the source code the only two things worth of mentioning are:
- The word **pipe** which appears in the style section and it doesn't seem like a variable name.
- A hidden article which says **Acrostico Inicial**. And acrostic is a type of word puzzle or poem where the first, middle, or last letters of each line spell out a specific word or message when read vertically.

Let's try to make a phrase with the initial letters of each article.

This are the initials of each article:
W-I-N-S-E-R-V-E-R-R-O-O-T-F-A-K-E-N-E-W-S

Which read: Win Server Root Fake News.

Now, the SSH service is also open, so we will try to bruteforce our way in using pipe as the username.

![](IMAGES/Winfake%20-%20IMG_09.png)

We found that pipe is, indeed, a username on the system and it's password is *kisses*.

![](IMAGES/Winfake%20-%20IMG_10.png)

And we have gained access to a PowerShell terminal as the user "pipe" but the system is still a linux.

Now, the acrostic might be a possible root password, so let's try it.

![](IMAGES/Winfake%20-%20IMG_11.png)

We try possible variations of upper and lower cases and we root gain access with : WinServerRootFakeNews.

---