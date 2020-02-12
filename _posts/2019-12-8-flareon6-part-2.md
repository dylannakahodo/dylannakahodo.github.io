---
layout: post
title: "2019 Flare-on Challenge Writeups: Challenge 3"
permalink: /posts/flareon6-part-2
category: CTF
---

## Challenge 3
## flarebear.apk
### Description
> We at Flare have created our own Tamagotchi pet, the flarebear. He is very fussy. Keep him alive and happy and he will give you the flag.

### Walkthrough
In this challenge, we are given the file, flarebear.apk. I run the following command to convert the apk to a jar.

```
d2j-dex2jar.exe .\flarebear.apk
```

Next, I open up [jd-gui](https://java-decompiler.github.io/) in order to decompile the jar file. Once open, I explore the different folders, the most interesting one being `com.fireeye.flarebear`, which contains the source code for the flarebear app.

<p align="center" markdown="1">
![JDGui](/assets/flareon6_2019/Challenge3/JD-GUI.png)
</p>


I expand the `FlareBearActivity` class and look for any interesting methods.

<p align="center" markdown="1">
![FlareBearActivity](/assets/flareon6_2019/Challenge3/FlareBearActivity.png)
</p>

The method that sticks out the most is `danceWithFlag()`, so I look to see where its called. I find that it gets called in the `setMood()` method, which also calls the `isHappy()` and `isEcstatic()` methods. 

<p align="center" markdown="1">
![setMoodFunction](/assets/flareon6_2019/Challenge3/setMood.png)
</p>

The `danceWithFlag()` method only gets called if `isEcstatic()` returns True, so I explore that 
first.

<p align="center" markdown="1">
![isEcstaticFunction](/assets/flareon6_2019/Challenge3/isEcstatic.png)
</p>

Great, so it looks like this returns True if the three stats (Mass, Happy, Clean) have the following values.

- Mass is 72
- Happy is 30
- Clean is 0

The next step is to figure out how these values are set, but before I continue analyzing the code, I decide to run the app to get a feel for it. Upon starting up the app, you're greeted with a basic start screen and the option to name your bear. The main screen has three buttons you can click, each representing the three actions you can take, "Feed", "Play", and "Clean". At this point, I go back to the code.

<p align="center" markdown="1">
![FlareBearMain](/assets/flareon6_2019/Challenge3/FlareBearMainScreen.png)
</p>

I find the three methods that correspond with the three actions, `feed()`, `play()`, and `clean()`. 

<p align="center" markdown="1">
![feedFunction](/assets/flareon6_2019/Challenge3/feed.png)
</p>

<p align="center" markdown="1">
![playFunction](/assets/flareon6_2019/Challenge3/play.png)
</p>

<p align="center" markdown="1">
![cleanFunction](/assets/flareon6_2019/Challenge3/clean.png)
</p>

Each action you take affects the three stats, so we have system of equations with three unknowns - the number of actions required to get to the Ecstatic state. I've summarized the the changes each action has on the stats below.

__Feed__
- +10 Mass
- +2 Happy
- -1 Clean

__Play__
- -2 Mass
- +4 Happy
- -1 Clean

__Clean__
- +0 Mass
- -1 Happy
- +6 Clean

This gives us the following three equations.

$$ Mass = 10 * feed - 2 * play + 0 * clean $$

$$ Happy = 2 * feed + 4 * play - 1 * clean $$

$$ Clean = - 1 * feed - 1 * play + 6 * clean $$

Plugging in the desired final stats and solving.

$$ 72 = 10 * feed - 2 * play + 0 * clean $$

$$ 30 = 2 * feed + 4 * play - 1 * clean $$

$$ 0 = - 1 * feed - 1 * play + 6 * clean $$

$$ Feed = 8 $$

$$ Play = 4 $$

$$ Clean = 2 $$

Jumping back to our emulator, making a new bear, and hitting each action the required number of times leads us to an ecstatic bear and the flag.

<p align="center" markdown="1">
![win](/assets/flareon6_2019/Challenge3/Win.png)
</p>

Flag for Challenge 3: `th4t_was_be4rly_a_chall3nge@flare-on.com`