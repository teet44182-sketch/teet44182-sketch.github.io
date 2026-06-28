---
title: Hack The Box - Oopsie Writeup
categories: [CYBERSECURITY, PENTEST]
tags: [HTB, WEB, PATH-HIJACK, PRIVESC]
---

## **Enumeration**

Initiate recon by **nmap** command:

![Icon](/assets/img/Oopsie/icon.png)


![Nmap Scan Result](/assets/img/Oopsie/nmap.png)

 **Opened port**

- 22  ssh port
- 80 http port
  
After inspect the home page source code, we found an url that is use to login.
  
  ![homepage|446](/assets/img/Oopsie/inspect_homepage.png)

Exploitation
There is an IDOR vunerbility on web browser url that enable us to access admin's access token and username. 
![Nmap Scan Result](/assets/img/Oopsie/idor.png)