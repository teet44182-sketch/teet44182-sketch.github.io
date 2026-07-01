---
title: Hack The Box - Archetype Writeup
categories: [CYBERSECURITY, PENTEST]
tags: [HTB, PRIVESC, PASSWORD_CRACKING]
---
 # **Archetype**

![icon](/assets/img/Archetype/icon.png)


## **Enumeration**

Initiate reconnaissance by **nmap** command :

![nmap](/assets/img/Archetype/nmap.png)

Opened port

- 135 msrpc port
- 139 netbios-ssn port
- 445 microsoft-ds port(smb)
- 1433 mssql port
- 5985 Window remote management(Winrm) port

We can gather information about files that shared through system via smb service called **smbclient**, in order to that we want to know name of share folders first.
```smbclient -L //ip-address/ ```
![smb](/assets/img/Archetype/smb.png)



