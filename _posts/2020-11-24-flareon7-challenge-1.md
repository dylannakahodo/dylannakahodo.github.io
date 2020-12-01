---
layout: post
title: "2020 Flare-On Challenge 1 Writeup: Fidler"
permalink: posts/flareon7-challenge-1
category: ctf
tags: [reverse-engineering, python, flareon7]
---

## Challenge 1 - Fidler
## Description
> Welcome to the Seventh Flare-On Challenge!
>
>This is a simple game. Win it by any means necessary and the victory screen will reveal the flag. Enter the flag here on this site to score and move on to the next level.
>
>This challenge is written in Python and is distributed as a runnable EXE and matching source code for your convenience. You can run the source code directly on any Python platform with PyGame if you would prefer.

## Contents
- [Tools Used](#tools-used)
- [Solution](#solution)
    - [Intro](#intro)
    - [fidler.py - decode_flag and victory_screen](#fidlerpy---decode_flag-and-victory_screen)
    - [Token Calculation](#token-calculation)
    - [Solution Script](#solution-script)
    - [Flag](#flag)

## Tools Used
- Python 3/IDLE

## Solution
### Intro
> tl;dr: Find victory condition in source code, run `decode_flag` with hard-coded victory condition. 

In this challenge we were given the following directories and files:

Directories:
- fonts
- img

Files: 
- controls.py
- fidler.exe
- fidler.py 

`fonts` and `img` contained the various assets for the game, `controls.py` looks like it handles events in the UI, `fidler.exe` is the game executable, and `fidler.py` contains the python source code for the game.

### fidler.py - decode_flag and victory_screen
```python
def decode_flag(frob):
    last_value = frob
    encoded_flag = [1135, 1038, 1126, 1028, 1117, 1071, 1094, 1077, 1121, 1087, 1110, 1092, 1072, 1095, 1090, 1027,
                    1127, 1040, 1137, 1030, 1127, 1099, 1062, 1101, 1123, 1027, 1136, 1054]
    decoded_flag = []

    for i in range(len(encoded_flag)):
        c = encoded_flag[i]
        val = (c - ((i%2)*1 + (i%3)*2)) ^ last_value
        decoded_flag.append(val)
        last_value = c

    return ''.join([chr(x) for x in decoded_flag])


def victory_screen(token):
    screen = pg.display.set_mode((640, 160))
    clock = pg.time.Clock()
    heading = Label(20, 20, 'If the following key ends with @flare-on.com you probably won!',
                    color=pg.Color('gold'), font=pg.font.Font('fonts/arial.ttf', 22))
    flag_label = Label(20, 105, 'Flag:', color=pg.Color('gold'), font=pg.font.Font('fonts/arial.ttf', 22))
    flag_content_label = Label(120, 100, 'the_flag_goes_here',
                               color=pg.Color('red'), font=pg.font.Font('fonts/arial.ttf', 32))

    controls = [heading, flag_label, flag_content_label]
    done = False

    flag_content_label.change_text(decode_flag(token))

    while not done:
        for event in pg.event.get():
            if event.type == pg.QUIT:
                done = True
            for control in controls:
                control.handle_event(event)

        for control in controls:
            control.update()

        screen.fill((30, 30, 30))
        for control in controls:
            control.draw(screen)

        pg.display.flip()
        clock.tick(30)
```
The decode_flag function calculates the final flag using `frob`, which is some token that gets passed to the `victory_screen` function. But how is the token calculated?

### Token Calculation
```python
while not done:
        target_amount = (2**36) + (2**35)
        if current_coins > (target_amount - 2**20):
            while current_coins >= (target_amount + 2**20):
                current_coins -= 2**20
            victory_screen(int(current_coins / 10**8))
            return
```
In the `game_screen` function in fidler.py, we find that the token is calculated based on the `current_coins` and `target_amount` values. We now have enough information to calculate the flag.

### Solution Script
```python
def decode_flag(frob):
    last_value = frob
    encoded_flag = [1135, 1038, 1126, 1028, 1117, 1071, 1094, 1077, 1121, 1087, 1110, 1092, 1072, 1095, 1090, 1027,
                    1127, 1040, 1137, 1030, 1127, 1099, 1062, 1101, 1123, 1027, 1136, 1054]
    decoded_flag = []

    for i in range(len(encoded_flag)):
        c = encoded_flag[i]
        val = (c - ((i%2)*1 + (i%3)*2)) ^ last_value
        decoded_flag.append(val)
        last_value = c

    return ''.join([chr(x) for x in decoded_flag])

target_amount = (2**36) + (2**35)
token = int(target_amount / 10**8)
print(decode_flag(token))
```

### Flag
Running the above script prints out our flag: `idle_with_kitty@flare-on.com`

