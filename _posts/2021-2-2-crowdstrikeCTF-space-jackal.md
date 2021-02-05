---
layout: post
title: "Crowdstrike CTF: Space Jackal Writeup"
permalink: /posts/crowdstrikeCTF-space-jackal-writeup
category: CTF
tags: [osint]
---
Space Jackal is a hacktivist adversary.
> Not to be confused with spaceflight enthusiasts, SPACE JACKAL have very strict opinions on source code indentation. Brought together by their unbounded hate for ASCII character 9, they will not rest until the last tab stop has been eradicated from the face of the Internet.
<p align="center" markdown="1">
![SpaceJackal](/assets/crowdstrikeCTF/spacejackal.jpg)
</p>

## The Proclamation
### Description
<p align="center" markdown="1">
![TheProclamationDescription](/assets/crowdstrikeCTF/the-proclamation-description.png)
</p>

>A mysterious file appeared on a deep dark web forum. Can you figure out what we can't see right now?

For this challenge, we're given a file called `proclamation.dat` ([Download](https://github.com/dylannakahodo/CTF-Resources/raw/main/Crowdstrike%20CTF/Space%20Jackal/proclamation.dat))

### Solution
The file is a DOS/MBR boot sector.
```
$ file proclamation.dat
proclamation.dat: DOS/MBR boot sector
```

Running the file with qemu displays the following message:
<p align="center" markdown="1">
![ProclamationMessage](/assets/crowdstrikeCTF/proclamation-message.png)
</p>

#### Debugging with GDB
Next, I run  the following command so I can debug the bootloader with GDB.
```
$ qemu-system-x86_64 -s -S proclamation.dat
```
`-s`: is shorthand for `-gdb tcp::1234` which says to start a gdb server listening on TCP port 1234

`-S`: prevents the CPU from starting which gives GDB time to connect


Then I connect to the GDB server:
```
$ gdb
(gdb) target remote localhost:1234
(gdb) break *0x7c00
(gdb) cont
```

I set a breakpoint at 0x7c00 because that's when the bootloader is loaded into memory and begins executing.

After stepping through the execution a bit, I found that the bootloader reads bytes starting from 0x7C78, performs some operations on it, and the result is what gets displayed. 

I copy a good amount of bytes from 0x7C78 and wrote a short python script to solve.

#### Solution Script
```python
blob = 
"2ebfc68685c4cabd8fca8b988fca8685858183848dca8c8598ca82838d828693ca83849e8f8686838d8f849ee08
3848e839c838e9f8b8699c4cabe85ca8c83848eca9e828f87c6ca9d8fca828b9c8fca8e8f9c83998f8ee08bca9e8
f999ec4e0e0be828f988fca8399ca8bca878f99998b8d8fca82838e8e8f84ca8384ca9e828399ca8885859e86858
b8e8f98c4e0e0ac83848eca839ec6ca8b848eca839eca9d838686ca868f8b8eca93859fca8584ca9e828fca98858
b8eca9e85e08c83848e83848dca9f99c4cabd8fca86858581ca8c85989d8b988eca9e85ca878f8f9eca9e828fe08
c8f9dca9e828b9eca9d838686ca878b818fca839eca8b8686ca9e828fca9d8b93ca9e8298859f8d82c4e0e0ad858
58eca869f8981c6ca8b848eca988f878f87888f98d0e0cacacacabd8fca86859c8fca999a8b898f99ca879f8982c
a8785988fca9e828b84ca9e8b8899cbea811911a9b991da988ed998b5da8cb5da92d8dab588dada9e86da8b8ed99
89797eaf4f4f4f4f4f4f4f4f4f4f4f4f4f4f4f4f4f4f455aa"

edx = 0x9
flag = ''

for i in range(0,len(blob),2):
    al = blob[i:i+2]
    edx = edx << 2
    edx += 0x42
    edx = edx & 0xff
    al = int(al,16) ^ edx
    flag += chr(al)
print(flag)
```

#### Output
```
Hello. We are looking for highly intelligent
individuals. To find them, we have devised
a test.

There is a message hidden in this bootloader.

Find it, and it will lead you on the road to
finding us. We look forward to meet the
few that will make it all the way through.

Good luck, and remember:
    We love spaces much more than tabs!kóûCS{0rd3r_0f_0x20_b00tl0ad3r}}¿@
```
Flag: `CS{0rd3r_0f_0x20_b00tl0ad3r}`