---
layout: post
title: "2019 Flare-on Challenge Writeups: Challenge 4"
permalink: /posts/flareon6-part-3
category: CTF
---

## Challenge 4 
## DNS Chess
### Description
> Some suspicious network traffic led us to this unauthorized chess program running on an Ubuntu desktop. This appears to be the work of cyberspace computer hackers. You'll need to make the right moves to solve this one. Good luck!

### Walkthrough
In Challenge 4, we're given the following 3 files.
- capture.pcap
- ChessAI.so
- ChessUI

From the challenge description, we can assume that the two Chess files are ELF binaries. I boot up my Kali Linux VM and run the `file` command to double check.

<p align="center" markdown="1">
![ChessAIFile](/assets/flareon6_2019/Challenge4/ChessAIFile.png)
</p>

<p align="center" markdown="1">
![ChessUIFile](/assets/flareon6_2019/Challenge4/ChessUIFile.png)
</p>

The `ChessUI` program is a game of chess against a computer opponent called DeepFLARE and we're prompted to make the first move.

<p align="center" markdown="1">
![ChessBlaster 3000](/assets/flareon6_2019/Challenge4/ChessBlaster3000.png)
</p>

Though no matter what move we make, the game ends with DeepFLARE resigning.

<p align="center" markdown="1">
![Resign](/assets/flareon6_2019/Challenge4/Resign.png)
</p>

I switch gears and open up `capture.pcap` in Wireshark. Looks like a bunch of DNS lookups that describe chess moves.

<p align="center" markdown="1">
![Wireshark](/assets/flareon6_2019/Challenge4/Wireshark.png)
</p>

I run the following command to extract all of the DNS responses from the pcap.

```bash
tshark -r capture.pcap -T fields -e dns.a -e dns.qry.name -2R "dns.flags.response eq 1" 
```

DNS Responses:
```
127.150.96.223,127.0.0.1        rook-c3-c6.game-of-thrones.flare-on.com
127.252.212.90,127.0.0.1        knight-g1-f3.game-of-thrones.flare-on.com
127.215.177.38,127.0.0.1        pawn-c2-c4.game-of-thrones.flare-on.com
127.118.118.207,127.0.0.1       knight-c7-d5.game-of-thrones.flare-on.com
127.89.38.84,127.0.0.1  bishop-f1-e2.game-of-thrones.flare-on.com
127.109.155.97,127.0.0.1        rook-a1-g1.game-of-thrones.flare-on.com
127.217.37.102,127.0.0.1        bishop-c1-f4.game-of-thrones.flare-on.com
127.49.59.14,127.0.0.1  bishop-c6-a8.game-of-thrones.flare-on.com
127.182.147.24,127.0.0.1        pawn-e2-e4.game-of-thrones.flare-on.com
127.0.143.11,127.0.0.1  king-g1-h1.game-of-thrones.flare-on.com
127.227.42.139,127.0.0.1        knight-g1-h3.game-of-thrones.flare-on.com
127.101.64.243,127.0.0.1        king-e5-f5.game-of-thrones.flare-on.com
127.201.85.103,127.0.0.1        queen-d1-f3.game-of-thrones.flare-on.com
127.200.76.108,127.0.0.1        pawn-e5-e6.game-of-thrones.flare-on.com
127.50.67.23,127.0.0.1  king-c4-b3.game-of-thrones.flare-on.com
127.157.96.119,127.0.0.1        king-c1-b1.game-of-thrones.flare-on.com
127.99.253.122,127.0.0.1        queen-d1-h5.game-of-thrones.flare-on.com
127.25.74.92,127.0.0.1  bishop-f3-c6.game-of-thrones.flare-on.com
127.168.171.31,127.0.0.1        knight-d2-c4.game-of-thrones.flare-on.com
127.148.37.223,127.0.0.1        pawn-c6-c7.game-of-thrones.flare-on.com
127.108.24.10,127.0.0.1 bishop-f4-g3.game-of-thrones.flare-on.com
127.37.251.13,127.0.0.1 rook-d3-e3.game-of-thrones.flare-on.com
127.34.217.88,127.0.0.1 pawn-e4-e5.game-of-thrones.flare-on.com
127.57.238.51,127.0.0.1 queen-a8-g2.game-of-thrones.flare-on.com
127.196.103.147,127.0.0.1       queen-a3-b4.game-of-thrones.flare-on.com
127.141.14.174,127.0.0.1        queen-h5-f7.game-of-thrones.flare-on.com
127.238.7.163,127.0.0.1 pawn-h4-h5.game-of-thrones.flare-on.com
127.230.231.104,127.0.0.1       bishop-e2-f3.game-of-thrones.flare-on.com
127.55.220.79,127.0.0.1 pawn-g2-g3.game-of-thrones.flare-on.com
127.184.171.45,127.0.0.1        knight-h8-g6.game-of-thrones.flare-on.com
127.196.146.199,127.0.0.1       bishop-b3-f7.game-of-thrones.flare-on.com
127.191.78.251,127.0.0.1        queen-d1-d6.game-of-thrones.flare-on.com
127.159.162.42,127.0.0.1        knight-b1-c3.game-of-thrones.flare-on.com
127.184.48.79,127.0.0.1 bishop-f1-d3.game-of-thrones.flare-on.com
127.127.29.123,127.0.0.1        rook-b4-h4.game-of-thrones.flare-on.com
127.191.34.35,127.0.0.1 bishop-c1-a3.game-of-thrones.flare-on.com
127.5.22.189,127.0.0.1  bishop-e8-b5.game-of-thrones.flare-on.com
127.233.141.55,127.0.0.1        rook-f2-f3.game-of-thrones.flare-on.com
127.55.250.81,127.0.0.1 pawn-a2-a4.game-of-thrones.flare-on.com
127.53.176.56,127.0.0.1 pawn-d2-d4.game-of-thrones.flare-on.com
```

I'll add these to my `/etc/hosts` file and retry the Chess game with these moves to see if anything different happens. First I clean up the output to remove all of the 127.0.0.1 addresses.

```
127.150.96.223        rook-c3-c6.game-of-thrones.flare-on.com
127.252.212.90        knight-g1-f3.game-of-thrones.flare-on.com
127.215.177.38        pawn-c2-c4.game-of-thrones.flare-on.com
127.118.118.207       knight-c7-d5.game-of-thrones.flare-on.com
127.89.38.84          bishop-f1-e2.game-of-thrones.flare-on.com
127.109.155.97        rook-a1-g1.game-of-thrones.flare-on.com
127.217.37.102        bishop-c1-f4.game-of-thrones.flare-on.com
127.49.59.14          bishop-c6-a8.game-of-thrones.flare-on.com
127.182.147.24        pawn-e2-e4.game-of-thrones.flare-on.com
127.0.143.11          king-g1-h1.game-of-thrones.flare-on.com
127.227.42.139        knight-g1-h3.game-of-thrones.flare-on.com
127.101.64.243        king-e5-f5.game-of-thrones.flare-on.com
127.201.85.103        queen-d1-f3.game-of-thrones.flare-on.com
127.200.76.108        pawn-e5-e6.game-of-thrones.flare-on.com
127.50.67.23          king-c4-b3.game-of-thrones.flare-on.com
127.157.96.119        king-c1-b1.game-of-thrones.flare-on.com
127.99.253.122        queen-d1-h5.game-of-thrones.flare-on.com
127.25.74.92          bishop-f3-c6.game-of-thrones.flare-on.com
127.168.171.31        knight-d2-c4.game-of-thrones.flare-on.com
127.148.37.223        pawn-c6-c7.game-of-thrones.flare-on.com
127.108.24.10         bishop-f4-g3.game-of-thrones.flare-on.com
127.37.251.13         rook-d3-e3.game-of-thrones.flare-on.com
127.34.217.88         pawn-e4-e5.game-of-thrones.flare-on.com
127.57.238.51         queen-a8-g2.game-of-thrones.flare-on.com
127.196.103.147       queen-a3-b4.game-of-thrones.flare-on.com
127.141.14.174        queen-h5-f7.game-of-thrones.flare-on.com
127.238.7.163         pawn-h4-h5.game-of-thrones.flare-on.com
127.230.231.104       bishop-e2-f3.game-of-thrones.flare-on.com
127.55.220.79         pawn-g2-g3.game-of-thrones.flare-on.com
127.184.171.45        knight-h8-g6.game-of-thrones.flare-on.com
127.196.146.199       bishop-b3-f7.game-of-thrones.flare-on.com
127.191.78.251        queen-d1-d6.game-of-thrones.flare-on.com
127.159.162.42        knight-b1-c3.game-of-thrones.flare-on.com
127.184.48.79         bishop-f1-d3.game-of-thrones.flare-on.com
127.127.29.123        rook-b4-h4.game-of-thrones.flare-on.com
127.191.34.35         bishop-c1-a3.game-of-thrones.flare-on.com
127.5.22.189          bishop-e8-b5.game-of-thrones.flare-on.com
127.233.141.55        rook-f2-f3.game-of-thrones.flare-on.com
127.55.250.81         pawn-a2-a4.game-of-thrones.flare-on.com
127.53.176.56         pawn-d2-d4.game-of-thrones.flare-on.com
```

After adding those entries to my `/etc/hosts` file, I started the game again. I tried a few of the opening moves I found in the DNS responses and it looks like the only valid opening move is `pawn-d2-d4`, I checked to see what IP address corresponds to that move and it is `127.53.176.56`. I made a note of that and moved on.

<p align="center" markdown="1">
![OpeningMove](/assets/flareon6_2019/Challenge4/OpeningMove.png)
</p>

After trying another legal move from the DNS responses, DeepFLARE again resigned. So it looks like we're limited to the moves from the DNS responses AND have to play them in a certain order. I guessed that I needed to complete the entire game of chess in order to get the flag. Instead of brute-forcing all of the chess moves, I decide to take a look at `ChessAI.so` to see how it decides what moves are considered valid.

I fire up [Ghidra](https://ghidra-sre.org/) and decompile `ChessAI.so`.

The most interesting function I find is the `getNextMove` function. 

<p align="center" markdown="1">
![getNextMove](/assets/flareon6_2019/Challenge4/getNextMove.png)
</p>

```cpp
ulong getNextMove(uint uParm1,char *pcParm2,uint uParm3,uint uParm4,uint *puParm5)

{
  char *pcVar1;
  hostent *phVar2;
  ulong uVar3;
  long in_FS_OFFSET;
  char local_58 [72];
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  strcpy(local_58,pcParm2);
  FUN_00101145(local_58,(ulong)uParm3,(ulong)uParm3);
  FUN_00101145(local_58,(ulong)uParm4,(ulong)uParm4);
  strcat(local_58,".game-of-thrones.flare-on.com");
  phVar2 = gethostbyname(local_58);

///////////////////////////////////////////////////////////////
```

The first part of the function builds up the hostname and stores it into `local_58`. It then uses [`gethostbyname()`](https://linux.die.net/man/3/gethostbyname) to perform a host lookup and saves a struct of type [_hostent_](https://www.gnu.org/software/libc/manual/html_node/Host-Names.html) in `phVar2`. 

```cpp
///////////////////////////////////////////////////////////////
    if ((((phVar2 == (hostent *)0x0) || (pcVar1 = *phVar2->h_addr_list, *pcVar1 != 0x7f)) ||
        ((pcVar1[3] & 1U) != 0)) || (uParm1 != ((uint)(byte)pcVar1[2] & 0xf))) {
        uVar3 = 2;
    }
    else {
        sleep(1);
        (&DAT_00104060)[(ulong)(uParm1 * 2)] = (&DAT_00102020)[(ulong)(uParm1 * 2)] ^ pcVar1[1];
        (&DAT_00104060)[(ulong)(uParm1 * 2 + 1)] = (&DAT_00102020)[(ulong)(uParm1 * 2 + 1)] ^ pcVar1[1];
        *puParm5 = (uint)((byte)pcVar1[2] >> 4);
        puParm5[1] = (uint)((byte)pcVar1[3] >> 1);
        strcpy((char *)(puParm5 + 2),(&PTR_s_A_fine_opening_00104120)[(ulong)uParm1]);
        uVar3 = (ulong)((byte)pcVar1[3] >> 7);
    }
    if (local_10 == *(long *)(in_FS_OFFSET + 0x28)) {
        return uVar3;
    }

```

The main things to focus on for this part of the function is the if statement. Here is a breakdown of the conditions that must be true:

* phVar2 == (hostent *)0x0
    - The host lookup must succeed. phVar2 must not contain a null pointer
* pcVar1 = *phVar2->h_addr_list, *pcVar1 != 0x7f
    - The first byte of the IP address must be 127.
* pcVar1[3] & 1U) != 0
    - The last bit of the IP address must be 0. (The IP address ends with an even number)
* uParm1 != ((uint)(byte)pcVar1[2] & 0xf
    - The last 4 bits of the 3rd byte of the IP address must equal uParm1 (the first parameter of the function)

With this criteria in mind, I return to the list of IP addresses and removed all of the IPs that ended with an odd number. I also labelled each IP with the lowest 4 bits of the IP's 3rd byte.

```
4 - 127.252.212.90        knight-g1-f3.game-of-thrones.flare-on.com
1 - 127.215.177.38        pawn-c2-c4.game-of-thrones.flare-on.com
6 - 127.89.38.84          bishop-f1-e2.game-of-thrones.flare-on.com
5 - 127.217.37.102        bishop-c1-f4.game-of-thrones.flare-on.com
b - 127.49.59.14          bishop-c6-a8.game-of-thrones.flare-on.com
3 - 127.182.147.24        pawn-e2-e4.game-of-thrones.flare-on.com
c - 127.200.76.108        pawn-e5-e6.game-of-thrones.flare-on.com
d - 127.99.253.122        queen-d1-h5.game-of-thrones.flare-on.com
a - 127.25.74.92          bishop-f3-c6.game-of-thrones.flare-on.com
8 - 127.108.24.10         bishop-f4-g3.game-of-thrones.flare-on.com
9 - 127.34.217.88         pawn-e4-e5.game-of-thrones.flare-on.com
e - 127.141.14.174        queen-h5-f7.game-of-thrones.flare-on.com
7 - 127.230.231.104       bishop-e2-f3.game-of-thrones.flare-on.com
2 - 127.159.162.42        knight-b1-c3.game-of-thrones.flare-on.com
0 - 127.53.176.56         pawn-d2-d4.game-of-thrones.flare-on.com
```
It looks like the third byte gives us the order of the moves!

Here is the final list of moves to beat the game.

```
pawn-d2-d4
pawn-c2-c4
knight-b1-c3
pawn-e2-e4
knight-g1-f3
bishop-c1-f4
bishop-f1-e2
bishop-e2-f3
bishop-f4-g3
pawn-e4-e5
bishop-f3-c6
bishop-c6-a8
pawn-e5-e6
queen-d1-h5
queen-h5-f7
```
<p align="center" markdown="1">
![Win](/assets/flareon6_2019/Challenge4/Win.png)
</p>

Flag for Challenge 4: `LooksLikeYouLockedUpTheLookupZ@flare-on.com`