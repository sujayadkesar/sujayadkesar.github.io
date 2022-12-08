---
title: Disk Analysis & Autopsy Write-up | TryHackMe
date: 2022-06-24 00:00:00 +530
categories: [Write Up, TryHackMe]
tags: [write up, tryhackme, ctf, blue team] # TAG names should always be lowercase
---

![Heading Image](/assets/img/thm/diskanalysis/head.png)

---

Connecting via rdp:

```
xfreerdp /v:10.10.245.192 /u:administrator /p:letmein123! /workarea /cert:ignore
```

after loading the .aut file in autospy, We can answer the questions.:P

In data sources,

![screenshot](/assets/img/thm/diskanalysis/img1.png)

What is the MD5 hash of the E01 image?

```
3f08c518adb3b5c1359849657a9b2079
```

In Results-> Extracted Content-> Operating System Information

![screenshot](/assets/img/thm/diskanalysis/img2.png)

What is the computer account name?

```
DESKTOP-0R59DJ3
```

In Results-> Extracted Content-> Operating System User Account

![screenshot](/assets/img/thm/diskanalysis/img3.png)

List all the user accounts. (alphabetical order)

```
H4S4N,joshwa,Keshav,sandhya,shreya,sivapriya srini,subu
```

![screenshot](/assets/img/thm/diskanalysis/img4.png)

Who was the last user to log into the computer?

```
sivapriya
```

![screenshot](/assets/img/thm/diskanalysis/img5.png)

What is the name of the network monitoring tool?

```
Look@LAN
```

hmm :p so there’s a file in its program location.

![screenshot](/assets/img/thm/diskanalysis/img6.png)

What was the IP address of the computer?

```
192.168.130.216
```

What was the MAC address of the computer? (XX-XX-XX-XX-XX-XX)

```
08-00-27-2c-c4-b9
```

![screenshot](/assets/img/thm/diskanalysis/img7.png)

Name the network cards on this computer.

```
Intel(R) PRO/1000 MT Desktop Adapter
```

![screenshot](/assets/img/thm/diskanalysis/img8.png)

![screenshot](/assets/img/thm/diskanalysis/img9.png)

A user bookmarked a Google Maps location. What are the coordinates of the location?

```
12°52'23.0"N 80°13'25.0"E
```

![screenshot](/assets/img/thm/diskanalysis/img10.png)

![screenshot](/assets/img/thm/diskanalysis/img11.png)

A user has his full name printed on his desktop wallpaper. What is the user’s full name?

```
Anto Joshwa
```

![screenshot](/assets/img/thm/diskanalysis/img12.png)

A user had a file on her desktop. It had a flag but she changed the flag using PowerShell. What was the first flag?

```
flag{HarleyQuinnForQueen}
```

![screenshot](/assets/img/thm/diskanalysis/img13.png)

The same user found an exploit to escalate privileges on the computer. What was the message to the device owner?

```
Flag{I-hacked-you}
```

Opening the youtube link :P

<iframe width="560" height="315" src="https://www.youtube.com/embed/C9GfMfFjhYI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

One of the tools is mimikatz as I have seen logs of it somewhere :p idr where.

![screenshot](/assets/img/thm/diskanalysis/img14.png)

and another from defender history: ( had gave up on this one. ;-; )

```
/img_HASAN2.E01/vol_vol3/ProgramData/Microsoft/Windows            Defender/Scans/History/Service/DetectionHistory/02/8363AFD9-AF2E-453A-8B2D-766E1C57A8BA
```

2 hack tools focused on passwords were found in the system. What are the names of these tools? (alphabetical order)

```
lazagne,mimikatz
```

![screenshot](/assets/img/thm/diskanalysis/img15.png)

![screenshot](/assets/img/thm/diskanalysis/img16.png)

There is a YARA file on the computer. Inspect the file. What is the name of the author?

```
Benjamin DELPY gentilkiwi
```

![screenshot](/assets/img/thm/diskanalysis/img17.png)

One of the users wanted to exploit a domain controller with an MS-NRPC based exploit. What is the filename of the archive that you found? (include the spaces in your answer)

```
2.2.0 20200918 Zerologon encrypted.zip
```
