---
layout: post
title: "2020 Flare-On Challenge 2 Writeup: Garbage"
permalink: posts/flareon7-challenge-2
category: ctf
tags: [reverse-engineering, windows, flareon7]
---

## Challenge 2 - Garbage
## Description
> One of our team members developed a Flare-On challenge but accidentally deleted it. We recovered it using extreme digital forensic techniques but it seems to be corrupted. We would fix it but we are too busy solving today's most important information security threats affecting our global economy. You should be able to get it working again, reverse engineer it, and acquire the flag.

## Contents
- [Tools Used](#tools-used)
- [Solution](#solution)
    - [Intro](#intro)
    - [Initial Analysis](#initial-analysis)
- [References](#references)

## Tools Used
- PE Explorer
- [PEBear](https://hshrzd.wordpress.com/pe-bear/)
- [UPX](https://upx.github.io/)
- [HxD]

## Solution
### Intro
> tl;dr: Fix corrupted executable by TODO

In this challenge we were given a single file, `garbage.exe`.

### Initial Analysis
`garbage.exe` is a PE file that has been packed with UPX.
<p align="center" markdown="1">
![FileInfo](/assets/flareon7_2020//garbage/FileInfo.png)
</p>

Attempting to run the file results in an error. Though this is expected, as the challenge description mentions the file being corrupted. Also, since the file is corrupted, it can't be unpacked just yet, we will need to fix it first.

<p align="center" markdown="1">
![StartError](/assets/flareon7_2020//garbage/StartError.png)
</p>

Let's open up the file in a hex editor and take a look.

<p align="center" markdown="1">
![PartialApplicationManifest](/assets/flareon7_2020//garbage/PartialApplicationManifest.png)
</p>

Scrolling to the bottom we can see that part of the [application manifest](https://docs.microsoft.com/en-us/windows/win32/sbscs/application-manifests) has been cut off.

>An **application manifest** is an XML file that describes and identifies the shared and private side-by-side assemblies that an application should bind to at run time. These should be the same assembly versions that were used to test the application. Application manifests may also describe metadata for files that are private to the application.

I copy-pasted an example manifest I found over the corrupted one and saved the file as `garbage_fixed_manifest.exe`, but unfortunately the program still doesn't run or unpack.

<p align="center" markdown="1">
![FixedApplicationManifest](/assets/flareon7_2020//garbage/FixedApplicationManifest.png)
</p>

Opening `garbage_fixed_manifest.exe` in PE-Bear and viewing the Section Headers reveals the issue.

<p align="center" markdown="1">
![SectionHeaders](/assets/flareon7_2020//garbage/SectionHeaders.png)
</p>

Notice how the `Raw Size` for the `.rsrc` section is colored red? This indicates the `Raw Size` is wrong, double clicking the cell shows the expected value of `400`. 

To fix the corrupted file, I padded the end of the file with `0x400-0x244` (0x1BC) null bytes.

Now the binary runs, but we get a different error...

<p align="center" markdown="1">
![SideBySideError](/assets/flareon7_2020//garbage/SideBySideError.png)
</p>

The next thing I try is unpacking the binary with UPX and once again opening it in PE-Bear.

I unpack the binary with the following command: `upx.exe -d .\garbage_fixed_manifest.exe`

