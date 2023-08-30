---
title: <img width="50" height="50" alt="front-page port 80-shoopyu" src="https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/2f1f476d-0772-44dd-9cc2-e95aeb3eeb88"> HTTP Rate Limit Bypass - Bug Bounty Methodology
date: 2023-03-23 00:00:02 +730
categories: [Resources, bugbounty]
tags: [http rate limit bypass, bug bounty, rate limiting, bypass] # TAG names should always be lowercase
mermaid: true

---

![img](https://www.akana.com/sites/default/files/image/2021-01/image-blog-akana-owasp.jpg){: .dark .w-75 .shadow .rounded-10 w='1212' h='668' }

# Bug Bounty Methodology: HTTP Rate Limit Bypass


## Introduction

Hey there, fellow bug bounty hunters! Today, we're going to explore a fun and sneaky bug bounty methodology that involves bypassing those  HTTP rate limits. We'll learn how to outsmart the servers by adding some secret headers and playing around with IP addresses. So grab your Burp Suite and get ready for some ninja moves!

## Background

Rate limiting is like a security guard for servers, trying to prevent abuse and excessive requests. When you cross the line, the server slaps you with a `429` HTTP status code, saying, "Whoa there, buddy, too many requests!" But guess what? We can find ways to dance around those limits.

```mermaid
sequenceDiagram
    participant Hacker
    participant Server
    participant BurpSuite

    Hacker->>BurpSuite: Set up Burp Suite
    Hacker->>BurpSuite: Configure browser proxy
    Hacker->>Server: Send initial request
    BurpSuite->>Server: Intercept request
    Server-->>BurpSuite: Respond with 429 status code, red
    BurpSuite->>Hacker: Intercepted 429 response, red

    loop Modify Headers & IP
        Hacker->>BurpSuite: Modify headers and IP, blue
        BurpSuite->>Server: Forward modified request, blue
        Server-->>BurpSuite: Respond with modified response, green
        BurpSuite->>Hacker: Intercept modified response, green
        alt Rate Limit Bypassed?
            Hacker->>Hacker: Celebrate bypass success!, #00FF00
        else
            Hacker->>Hacker: Analyze response and iterate, #FFA500
        end
    end

```

## Bypass Techniques

To outsmart rate limiting, we'll use some clever headers that let us pretend to be different clients with fancy IP addresses. Here are the secret headers we'll play with:

1.  `X-Forwarded-For`

2.  `X-Originating-IP`

3.  `X-Remote-Addr`

4.  `X-Remote-IP`

5.  `X-Client-IP`

6.  `X-Forwarded`

7.  `X-Forwarded-Host`

8.  `X-Host`

9.  `X-Forwarded-Server`
10.  `X-Real-IP`

## Performing the Test using Burp Suite

Now it's time to unleash the power of Burp Suite, our trusty ally in the bug bounty hunt. Follow these steps to perform the rate limit bypass test:

1.  **Set up Burp Suite**: Download and install [Burp Suite](https://portswigger.net/burp) on your system. Make your browser use Burp Suite as a proxy. Burp Suite is our ninja gear!

2.  **Intercept the request**: Launch Burp Suite, go to the target application, and intercept the request you want to test. Just click on "Proxy" -> "Intercept," and make sure the "Intercept is on" button is enabled. We're ready to strike!

3.  **Modify the headers**: In the "Intercept" tab of Burp Suite, find the request headers and add the secret bypass headers we learned earlier. Change the IP address in the header value to a different IP for each subsequent request. We're pulling off the header heist!

    Example:

    <br>
    
    ```
    GET /path/to/resource HTTP/1.1
    Host: example.com
    X-Forwarded-For: 192.168.0.1
    ```
    
4.  **Forward the modified request**: After modifying the headers, click the "Forward" button in Burp Suite to send the modified request to the server. It's time to strike with our secret weapons!

5.  **Analyze the response**: Examine the server's response to see if we successfully bypassed the rate limiting. If the server doesn't respond with a 429 status code, it means we did it! We're the rate limit ninjas!

6.  **Repeat the process**: Keep going! Repeat steps 3 to 5, changing the IP address and headers for each subsequent request. Each attempt brings us closer to bypassing the rate limiting. We're persistent and clever!

> **Note:** The exact steps and appearance of Burp Suite may vary depending on the version you're using. Check out the Burp Suite documentation for detailed instructions.
{: .prompt-warning }


## Conclusion

Bypassing rate limiting is like a thrilling dance in the world of bug bounty hunting. With our secret headers and IP address tricks, we can attempt to outsmart the servers. But remember, be responsible and stay within legal and ethical boundaries. Happy bug hunting, my fellow ninjas!

![the-end](https://media.giphy.com/media/DAtJCG1t3im1G/giphy.gif)
