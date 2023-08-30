---
title: <img width="50" height="50" alt="front-page port 80-shoopyu" src="https://user-images.githubusercontent.com/95465072/209355489-9dc22e00-44a6-4aa3-a642-47bfbd5be517.png"> Illumination | HackTheBox | Forensics Challange
date: 2022-12-11 00:00:02 +310
categories: [Write Up, HackTheBox]
tags: [write up, hackthebox, ctf, easy, walkthrough, web, http, sqli, docker, forensics,github,git,json,version-control, shoppy,  machine] # TAG names should always be lowercase

---

-------------------

<img width="963" alt="Screenshot 2022-12-23 201912" src="https://user-images.githubusercontent.com/95465072/209354935-a3978ab2-07e2-463e-8718-b83e45665189.png">

# Illumination
   

## Files Provided

There's only one file is provided which has sub directories as follows 
```
|--Illumination.JS
   |-------.git
   |-------config.json
   |-------bot.js
  ```

<img width="422" alt="ls -a" src="https://user-images.githubusercontent.com/95465072/209355022-184da004-b58d-4962-ad93-f771f400e104.png">

``` cat config.json```
```
{
        "token": "Replace me with token when in use! Security Risk!",
        "prefix": "~",
        "lightNum": "1337",
        "username": "UmVkIEhlcnJpbmcsIHJlYWQgdGhlIEpTIGNhcmVmdWxseQ==",
        "host": "127.0.0.1"
}
```

Hmmm, that top line is interesting. The token has been replaced for a security risk. There is a chance a developer has left this in the git logs. That’s what we can look at next. To do this just traverse in the terminal into the folder holding the `.git`. This is what you’ll see when you use the command:

```sh
git log
```

```
commit edc5aabf933f6bb161ceca6cf7d0d2160ce333ec (er)
Author: SherlockSec <dan@lights.htb>
Date:   Fri May 31 14:16:43 2019 +0100

    Added some whitespace for readability!

commit 47241a47f62ada864ec74bd6dedc4d33f4374699
Author: SherlockSec <dan@lights.htb>
Date:   Fri May 31 12:00:54 2019 +0100

    Thanks to contributors, I removed the unique was a security risk. Thanks for reporting respons

commit ddc606f8fa05c363ea4de20f31834e97dd527381
Author: SherlockSec <dan@lights.htb>
Date:   Fri May 31 09:14:04 2019 +0100

    Added some more comments for the lovely contrnks for helping out!

commit 335d6cfe3cdc25b89cae81c50ffb957b86bf5a4a
Author: SherlockSec <dan@lights.htb>
Date:   Thu May 30 22:16:02 2019 +0100           
                                                 
    Moving to Git, first time using it. First Com
```

I tried with all and 2nd on looks  intresting 

```sh
git show 47241a47f62ada864ec74bd6dedc4d33f4374699
```

```
$ git show 47241a47f62ada864ec74bd6dedc4d33f4374699
commit 47241a47f62ada864ec74bd6dedc4d33f4374699
Author: SherlockSec <dan@lights.htb>
Date:   Fri May 31 12:00:54 2019 +0100

    Thanks to contributors, I removed the unique token as it was a security risk. Thanks for reporting responsibly!

diff --git a/config.json b/config.json
index 316dc21..6735aa6 100644
--- a/config.json
+++ b/config.json
@@ -1,6 +1,6 @@
 {
 
-       "token": "SFRCe3YzcnNpMG5fYzBudHIwbF9hbV9JX3JpZ2h0P30=",
+       "token": "Replace me with token when in use! Security Risk!",
        "prefix": "~",
        "lightNum": "1337",
        "username": "UmVkIEhlcnJpbmcsIHJlYWQgdGhlIEpTIGNhcmVmdWxseQ==",
```

### Decode it through Base64 to get the flag!

# Congragulations!!

## Flag 
```HTB{v3rsi0n_c0ntr**************}```
