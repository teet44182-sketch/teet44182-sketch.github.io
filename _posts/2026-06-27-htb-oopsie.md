---
title: Hack The Box - Oopsie Writeup
categories: [CYBERSECURITY, PENTEST]
tags: [HTB, WEB, PATH-HIJACK, PRIVESC]
---

## **Enumeration**

เริ่มต้นด้วยการสแกนพอร์ตโดยใช้ Nmap เพื่อตรวจดูบริการที่เปิดอยู่บนเครื่องเป้าหมาย:

![Icon](/assets/img/Oopsie/icon.png)


![Nmap Scan Result](/assets/img/Oopsie/nmap.png)

 ### Opened port

- 22  ssh port
- 80 http port