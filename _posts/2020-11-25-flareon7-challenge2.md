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
- [References](#references)

## Tools Used
- PE Explorer
- [PEBear](https://hshrzd.wordpress.com/pe-bear/)
- [UPX](https://upx.github.io/)

## Solution
### Intro
> tl;dr: Fix corrupted executable by 