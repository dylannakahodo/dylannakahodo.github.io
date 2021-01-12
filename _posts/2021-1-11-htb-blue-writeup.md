---
layout: post
title: "Hack the Box: Blue Writeup"
permalink: /posts/htb-blue-writeup
category: CTF
tags: [hack-the-box, windows]
---

## TL;DR
Blue is a Windows box that is vulnerable to [EternalBlue](https://en.wikipedia.org/wiki/EternalBlue) and we can get SYSTEM access in less than 5 minutes.

## Recon and Enumeration
Start off by running `nmap` to see what ports are open:

```
sudo nmap -sC -sV 10.129.81.40
```

Right away, we can see that port 445 is open and that the box is running Windows 7 SP1, which makes me think of EternalBlue. (Also hinted by the name of this box).

<p align="center" markdown="1">
![NmapResults](/assets/HTB/Blue/nmap-results.png)
</p>

I want to double check that the box is vulnerable, so I run `vuln` scripts in `nmap`.


```
nmap -p445 --script vuln 10.129.81.40
```

<p align="center" markdown="1">
![VulnScan](/assets/HTB/Blue/vuln-scan.png)
</p>

## Exploitation and SYSTEM Access
After verifying the box is vulnerable to EternalBlue, the easiest way to exploit it is to use the built in modules in Metasploit. So I open up `msfconsole` and do a search for `eternalblue`.

<p align="center" markdown="1">
![Msfconsole](/assets/HTB/Blue/msfconsole-eternalblue.png)
</p>

For this attempt, I went with `exploit/windows/smb/ms17_010_eternalblue`. All I had to do was set `RHOSTS` to Blue's IP and `LHOST` to my IP and run the exploit.

After waiting about 30 seconds, I had a shell on the box with Admin privileges.

<p align="center" markdown="1">
![Proof](/assets/HTB/Blue/proof.png)
</p>