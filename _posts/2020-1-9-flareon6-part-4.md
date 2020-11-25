---
layout: post
title: "2019 Flare-on Challenge Writeups: Challenge 5 and Final Thoughts"
permalink: /posts/flareon6-part-4
category: CTF
tags: [reverse-engineering, windows, flareon6]
---

## Challenge 5
## demo
### Description
> Someone on the Flare team tried to impress us with their demoscene skills. It seems blank. See if you can figure it out or maybe we will have to fire them. No pressure.

### Walkthrough
In Challenge 5, we're given a single file.
- 4k.exe

I open up my Windows VM again and run the file. The program just displays a spinning FLARE logo, attempting to click or in anyway interact with the program just causes it to crash.

<p align="center" markdown="1">
![FlareLogo](/assets/flareon6_2019/Challenge5/FlareLogo.png)
</p>

Spoiler: The way I solved this challenge required no reverse engineering. In this program, there are two models drawn, one is the FLARE logo and the other is the flag. However, the z-coordinate for the flag is extremely high and ends up placing the model out of view. I got extremely lucky and decided to try dumping all of the models from the program during runtime first.

The way I achieved this was with two tools:
- NinjaRipper
    * Tool used to rip 3D models and textures from a running game
- Noesis with NinjaRipper Plugin
    * Tool to view 3D models

NinjaRipper is simple to use, just start it up, select the exe you want to rip, select an output directory, and hit "Run". Once the file is running, I hit the hotkey to rip all models.

<p align="center" markdown="1">
![NinjaRipper](/assets/flareon6_2019/Challenge5/NinjaRipper.png)
</p>

Finally, I open up Noesis and navigate to the directory where NinjaRipper dumped the models. Here, I see the two models, `Mesh_0000.rip` (FLARE logo) and `Mesh_0001.rip` (flag). 

<p align="center" markdown="1">
![flag](/assets/flareon6_2019/Challenge5/flag.png)
</p>

Flag for Challenge 5: `moar_pouetry@flare-on.com`

## Final Thoughts

Overall, I think I did pretty good, I definitely could have seen myself getting at least half of the challenges done if I had dedicated more time to this. I'm going to keep practicing and aim to finish all of the challenges in this year's competition. I'm extremely excited to see what the FLARE team puts out for 2020.