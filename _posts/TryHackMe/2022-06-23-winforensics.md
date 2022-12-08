---
title: Windows Forensics 1 Write-up | TryHackMe
date: 2022-06-23 00:00:00 +530
categories: [Write Up, TryHackMe]
tags: [write up, tryhackme, ctf, blue team] # TAG names should always be lowercase
---

![screenshot](/assets/img/thm/winforensics/head.png)

Since all the tasks till task 9 have straight easy questions from the information given in the section. The write up is only for the task 10.

connecting via rdp:

```shell
xfreerdp /u:THM-4n6 /p:123 /v:10.10.52.134 /cert:ignore /workarea
```

After opening Registry explorer, Loading the SAM files from:

```
C:\Users\THM-4n6\Desktop\triage\C\Windows\System32\config
```

![screenshot](/assets/img/thm/winforensics/1.png)

How many user created accounts are present on the system?

```
3 (UID in form 10xx are user created accounts)
```

What is the username of the account that has never been logged in?

```
thm-user2
```

What’s the password hint for the user THM-4n6?

```
count
```

loading the NTUSER.DAT for the THM-4n6 and looking in the `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs`.

![screenshot](/assets/img/thm/winforensics/2.png)

When was the file ‘Changelog.txt’ accessed?

```
2021-11-24 18:18:48
```

For the next question, we will look at UserAssist:

```
NTUSER.DAT\Software\Microsoft\Windows\Currentversion\Explorer\UserAssist\{GUID}\Count
```

What is the complete path from where the python 3.8.2 installer was run?

```
z:\setups\python-3.8.2.exe
```

checking the following registry locations:

`SYSTEM\CurrentControlSet\Enum\USBSTOR`

`SOFTWARE\Microsoft\Windows Portable Devices\Devices`

![screenshot](/assets/img/thm/winforensics/3.png)

![screenshot](/assets/img/thm/winforensics/4.png)

The GUID matches the kingston USB device,

When was the USB device with the friendly name ‘USB’ last connected?

```
2021-11-24 18:40:06
```

:P and done, Never been so easy.
