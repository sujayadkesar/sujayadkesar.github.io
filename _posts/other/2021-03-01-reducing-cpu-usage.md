---
title: Reducing cpu Usage in Windows | Guide
date: 2021-03-01 00:00:00 +530
categories: [Guides]
tags: [guide, windows, tweaks, gaming, performance] # TAG names should always be lowercase
---

> One of my older threads I made for nitksf.tech . I hope it helps.

# Some stuff in Windows

- Disable Superfetch in Windows [Link](https://www.technipages.com/windows-enable-disable-superfetch) <br/>Do this last, re-enable it if it has no effect.
- Windows Audio Device Graph Isolation, you could try turning it off: [howtogeek](https://www.howtogeek.com/273764/what-is-windows-audio-device-graph-isolation-and-why-is-it-running-on-my-pc/)
- In Windows 10, make sure Xbox GameDVR is disabled.
- Disable background apps in settings
- disable startup programs in task manager
- uninstall unnecessary software’s
- Unpark your cores (Windows 7 and 10) install [ParkControl](https://bitsum.com/parkcontrol/) Make sure parking is disabled and hit ‘Apply’, you may also want to adjust Frequency Scaling so it’s always giving 100%, change this and hit ‘Apply’. Bear in mind this will use more power as your CPU won’t dynamically alter clock speed.

![screenshot](/assets/img/other/reducing-cpu-usage/img1.png)

![screenshot](/assets/img/other/reducing-cpu-usage/img2.png)

# Something That I don’t recommend but I still do:

- Disable real-time protection of WIndows Defender(now called as windows security) from settings
- Disable Antimalware Service Executable service from services.msc
- Not having any Antivirus and Using a bit of brain while using internet can save a lot of cpu usage.
- you can try clean installing older windows (version 1903). It uses quite low cpu usage compared to current 2004 version. (I have heard this, tried but went back to updated win 10 as ms edge(chromium based) isn’t available in 1903 and it uses quite low ram and I kinda got used to it. This looks quite outdated as now we have win 11 but its still better than the updated version as this was the last version where you could actually disable win updates without itself turning up back on. (even when done via registry)

# In BIOS

- try increasing your memory frequency within your BIOS, the XMP profiles should make this easy. Google search for specific instructions for your motherboard.
  Please note to force constant max CPU performance (either by setting the Frequency Scaling to 100% in ParkControl, or by setting the ‘Minimum CPU’ state to 100% in your power settings: then keep C1E DISABLED and EIST ENABLED within your BIOS, you should be looking for something like this: (please check the edits at the bottom for more info on power states)

![screenshot](/assets/img/other/reducing-cpu-usage/img3.png)

![screenshot](/assets/img/other/reducing-cpu-usage/img4.png)

- If you’re using an older overclocked i5/i7 chip (2600k era etc.), you may see some benefits in disabling C3 and C6 power states in your BIOS.

### Properly Update Drivers

Sometimes if drivers are causing issues, there is a service called system interrupts which can cause high cpu and hdd usage so make sure all the drivers are perfectly installed. Use DDU uninstaller to reinstall and update Gpu drivers everytime to reduce conflicts between previous and current installation.

### Disable Shadowplay

To see how to disable ShadowPlay see this video:

<iframe width="560" height="315" src="https://www.youtube.com/embed/_7ej-SBLpzY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

If you have Amd GPU, Try disabling Amd relive.

# Some external Tips:

- If you have disabled all antivirus/antimalwares, you can do a monthly checkup using malwarebytes to be sure you don’t have any malicious stuff running,(uninstall malwarebytes back after scanning)
- You can do a weekly checkup using adwcleaner
- DISABLE WINDOWS UPDATE FFS by first stopping its service and then disabling it in services.msc. Update windows every 2–3 months by manually turning it on or just clean installing every major windows update(like I do), do it on your personal computers only. xD
  So this are some of the tweaks/changes that I have done in the past which have worked well .There are several other stuff that I don’t remember but this will be enough to give you a bit of boost in performance.Since my CPU is pretty old and bottlenecks my gpu hard, I had like 20–40fps improvement in valorant. games like Bf1 became playable again.
