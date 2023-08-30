---
title: <img width="50" height="50" alt="front-page port 80-shoopyu" src="https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/fec7f941-3c09-49dd-8f34-cb3a5befeaab">Suspicious Browser extension analysis 🔍
date: 2023-06-18 00:00:02 +730
categories: [Write Up, letsdefend]
comments: true
tags: [blueteam,cyberlabs,secops,security-operations, malware-analysis, reverse-engineering] # TAG names should always be lowercase


---

![Suspicious_Browser_Extension_Cover_4W5Om0Y](https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/fec7f941-3c09-49dd-8f34-cb3a5befeaab)
<br><br><br><br>
1.Which browser supports this extension?
With a simple google we can learn that crx files are associated with google chrome’s web browser
extensions. <br>
<img width="521" alt="1" src="https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/077124a7-5cd8-4320-8801-93a33e5ccfbc">
<br>
>ANSWER: Google Chrome
{: .prompt-info }

<br><br><br>
2.What is the name of the main file which contains metadata?
For this part we must unpack the file. CRX files are essentially just archived files similar to zip
files. So we can simply use any archive extractor tool such as unzip to extract the contents.<br>
<img width="407" alt="2" src="https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/c0d66542-f836-4b22-9ed2-1a530136e5a9">

Simple googling on what a manifest file reveals that it is common computing file which contains
metadata. Here we can prove this by reading the contents of manifest.json. It contains metadata
such as version, name, and even related files for the extension.<br>
<img width="282" alt="2-2" src="https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/34d569c5-106d-4fda-8075-a70600c74de3">
<br>
>ANSWER: manifest.json
{: .prompt-info }
<br><br><br>
3.How many js files are there?
Question 2 revealed that there were `2` javascript files: background.js and content.js.
<br><br><br>
4.Go to [crxcavator.io](https://crxcavator.io) and check if this browser extension has already been analysed by searching its name. Is it known to the community? 
Similiar to VirusTotal, crxcavator is a useful tool for checking if a browser extension was uploaded
and analysed. In this case, this is not known to the community.<br>
<img width="286" alt="4" src="https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/a9e427e1-6726-4c31-a579-8bc8342191d4">
<br>
>ANSWER: no
{: .prompt-info }

<br><br><br>
5.Download and install ExtAnalysis. Is the author of the extension known? <br>
For this task it is useful to move onto a more advanced tool called ExtAnalysis. Which can be
downloaded and installed from: https://github.com/Tuhinshubhra/ExtAnalysis. Once installed simply upload the crx file from the ‘upload extension’ tab.<br>
<img width="323" alt="5" src="https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/a6916277-5eb3-4ba3-87e6-c2199637b714">

Here we can see the author is unknown which is concerning.<br>
<img width="188" alt="5-2" src="https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/86aa2ae5-9918-490e-90ed-a105d989977f">
<br>
>ANSWER: No
{: .prompt-info }

<br><br><br>
6.Often there are URLs and domains in malicious extensions. Using ExtAnalysis, check the ‘URLs and Domains’ tab How many URLs & Domains are listed? <br>
We can see that there is 1 URL and 1 Domain which makes it 2 in total for this extension. However
it is important to note that obfuscated URLs/Domains will not be picked up by the tool, hence its
limitations when compared to manual analysis.<br>
<img width="339" alt="6" src="https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/7adcf162-fbe5-4065-8574-5964c0a8e0e2">
<br>
>ANSWER: 2
{: .prompt-info }
<br><br><br>
7.Find the piece of code that uses an evasion technique. Analyse it, what type of systems is it
attempting to evade?
In the view source code section it is possible to read the contents of each file. In this case, we will
read the contents of the ThankYou.html file.<br>
<img width="293" alt="7" src="https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/2db11a27-5fc5-4db9-bf67-e62a2f3dcdbe">

Here we see a strange if condition which seems to be checking the system for certain renderers such
as llvmpipe, swiftshader, virtualbox, and vmware. Some OSINT research reveals that these
renderers are associated with virtual machine usage. Furthermore, the ‘else if’ condition checks for
a certain color depth typically associated with virtual machines. The resulting action from both
blocks of if statements is the triggering of ‘chrome.processes.terminate(0)’ which indicates how
after the extension confirms it is being run in a virtual machine environment it will terminate to
prevent dynamic analysis.<br>
<img width="406" alt="7-2" src="https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/9434601c-4b20-4988-9eac-16300f8c2a82">
<br>
>ANSWER: Virtual Machine
{: .prompt-info }
<br><br><br>
8.If this type of system is detected what function is triggered in its response?
As mentioned earlier, ‘chrome.processes.terminate(0)’ will be triggered.
<br>
>ANSWER: chrome.processes.terminate(0)
{: .prompt-info }
<br><br><br>
9.What keyword in a user visited URL will trigger the if condition statement in the code?
Using https://deobfuscate.io/ we can see that the output shows a line ‘.url == str.match...’ with a
regex expression. Although the code is partially obsufucated still we can infer from this that it is
using a url type function with a regex expression to check if the user is on a page with the keyword
‘login’.<br>
<img width="377" alt="9" src="https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/b911fbc5-b8ab-4a98-86c3-192149eb14c4">
<br>
>ANSWER: login
{: .prompt-info }
<br><br><br>
10.Based on the analysis of the content.js, what type of malware is this?
Performing OSINT research on the readable functions such as ‘onkeydown’ tells us that it is used to
detect the triggering of key presses. We also see the variable name ‘key’ being used to store the key
presses/strokes. Correlating this fact with the previously known truth that this js script is using an if
statement to look for the regex condition of URLs which contain the keyword login, we can infer
that this is a type of keylogger malware.<br>
<img width="335" alt="10" src="https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/a78ed5f3-956e-4602-8232-95bd8b35f20d">
<br>
>ANSWER: keylogger
{: .prompt-info }
<br><br><br>
11.Which domain/URL will data be sent to?
Using the previous deobsufucation method for the background.js file we can see a new URL
domain which points to an obviously fake google website. Data is posted to this and the likely data
would be the user’s keystrokes as indicated in the ‘o’ variable declarations which mentions ‘key’.
<br>
>ANSWER: https://google-analytics-cm[.]com/analytics-3032344.txt
{: .prompt-info }
<br><br><br>
12. As a remediation measure, what type of credential would you recommend all affected
users to reset immediately
So we have established that the extension triggers keylogging capabilities when it detects the user

visiting any URL with the keyword ‘login’. These logs are then sent over to the actor’s typo-
squatting fake google website. Since many users were affected by this and have so far had their

accounts compromised it is wise to initiate a password reset for all users involved.<br>
<img width="408" alt="11" src="https://github.com/thelocalh0st/thelocalh0st.github.io/assets/125783410/9b84bc7d-9284-4f08-9845-31d9588933e0">
<br>
>ANSWER: password
{: .prompt-info }
