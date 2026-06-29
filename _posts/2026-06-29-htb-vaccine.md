---
title: Hack The Box - Vaccine Writeup
categories: [CYBERSECURITY, PENTEST]
tags: [HTB, WEB, SUID, PRIVESC]
---
 # **Vaccine**

![Icon](/assets/img/Vaccine/icon.png)
## **Enumeration**

Initiate reconnaissance by **nmap** command :

![nmap](/assets/img/Vaccine/nmap.png)

**Opened port**

- 21 ftp port
- 22 ssh port
- 80 http port

The first priority for me is **ftp** because we might get **anonymous** access without password
Luckily, got into ftp service. Next I start gathering anything that should benefits me.
Download a zip file call  **"backup.zip"** by **get** command.   ```backup ```

![ftp](/assets/img/Vaccine/ftp.png)

After that I try to unzip it but the problem is Password is needed.

![unzip0](/assets/img/Vaccine/unzip0.png)

To make **johntheripper** able to read and crack the password we need **zip2john**.

![zip2john](/assets/img/Vaccine/zip2john.png)

It's look like this when we finished :

Next, let john cracks the password by has rockyou.txt as reference.
```john --wordlist=/usr/share/wordlists/rockyou.txt backup```

![john](/assets/img/Vaccine/john.png)

We can obtain the result by ```john --show backup```.

![johnshow](/assets/img/Vaccine/johnshow.png)

Then unzip the file again now we have key for it.

![unzip2](/assets/img/Vaccine/unzip2.png)

Navigate through the php called **"index.php"** which is obtained from **backup.zip**.
Just a moment, found a credential for us :
```username = "admin" & password = "2cb42f8734ea607eefed3b70af13bbd3" ```

![index.php](/assets/img/Vaccine/index.php.png)


Save it as txt file : ```echo "2cb42f8734ea607eefed3b70af13bbd3" > admin.txt ```
We can identify its type by hash length of 32 characters which correspond to **MD5** hash. 
![admin.txt](/assets/img/Vaccine/qwerty.png)







