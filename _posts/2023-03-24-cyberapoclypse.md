---
title: <img width="50" height="50" alt="front-page port 80-shoopyu" src="https://user-images.githubusercontent.com/95465072/215008062-fa6a3eb8-8f2b-4c82-8d05-f7a81861edc9.png"> Hackthebox CyberApoclypse 2023 | The Cursed Mission
date: 2023-03-24 00:00:02 +240
categories: [Write Up, ctf]
tags: [write up, hackthebox, ctf,walkthrough,cyberapoclypse,cursed-mission] # TAG names should always be lowercase

---
&nbsp;

![enter image description here](https://user-images.githubusercontent.com/95465072/227514528-157c59bb-bc65-4dbe-b2f2-d4e29f9ae826.jpg)


# CyberApoclypse CTF 2023

## Forensic Challenge : `Roten`

### Description :
The iMoS is responsible for collecting and analyzing targeting data across various galaxies. The data is collected through their webserver, which is accessible to authorized personnel only. However, the iMoS suspects that their webserver has been compromised, and they are unable to locate the source of the breach. They suspect that some kind of shell has been uploaded, but they are unable to find it. The iMoS have provided you with some network data to analyse, its up to you to save us.

### Download Files:
[forensics_roten.zip](https://github.com/sujayadkesar/sujayadkesar.github.io/files/11061886/forensics_roten.zip)

#### 1️⃣ Open the `challenge.pcap` file with wireshark
`sudo wireshark challenge.pcap`

#### 2️⃣ Filter to http and then go to bottom
![enter image description here](https://user-images.githubusercontent.com/95465072/227515823-9cd9c27e-f95b-4c59-b14c-c48c8aa073a5.png)

![enter image description here](https://user-images.githubusercontent.com/95465072/227515845-23dd6d83-4a46-48e2-b695-956644b6f677.png)

#### 3️⃣ `map-update.php` has upload functionality and the malicious actor uploads a malicious php called `galacticmap.php`

![enter image description here](https://user-images.githubusercontent.com/95465072/227515849-edad97a6-ce16-4015-a13a-47f341f64f86.png)
&nbsp;

:warning: **Malicous PHP**
![enter image description here](https://user-images.githubusercontent.com/95465072/227515852-c6a39363-292b-4e6f-b37d-cf7d1a4be7c2.png)

#### 4️⃣ Decode the malicious PHP Code and print out
![enter image description here](https://user-images.githubusercontent.com/95465072/227515837-2e615e23-21fa-4163-945a-6e26a84549bb.png)


![enter image description here](https://user-images.githubusercontent.com/95465072/227515843-02d8d8d8-93c1-4b72-94c7-587d783924b7.png)

# HTB{W0w_R0t_A_DaY}
