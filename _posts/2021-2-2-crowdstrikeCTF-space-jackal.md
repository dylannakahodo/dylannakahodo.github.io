---
layout: post
title: "Crowdstrike CTF: Space Jackal Writeup"
permalink: /posts/crowdstrikeCTF-space-jackal-writeup
category: CTF
tags: [reverse-engineering, cryptography]
---
Space Jackal is a hacktivist adversary.
> Not to be confused with spaceflight enthusiasts, SPACE JACKAL have very strict opinions on source code indentation. Brought together by their unbounded hate for ASCII character 9, they will not rest until the last tab stop has been eradicated from the face of the Internet.
<p align="center" markdown="1">
![SpaceJackal](/assets/crowdstrikeCTF/spacejackal.jpg)
</p>

## Challenges
- [The Proclamation](#the-proclamation)
- [Matrix](#matrix)

## The Proclamation
### Description
<p align="center" markdown="1">
![TheProclamationDescription](/assets/crowdstrikeCTF/the-proclamation-description.png)
</p>

>A mysterious file appeared on a deep dark web forum. Can you figure out what we can't see right now?

For this challenge, we're given a file called `proclamation.dat` ([Download](https://github.com/dylannakahodo/CTF-Resources/raw/main/Crowdstrike%20CTF/Space%20Jackal/proclamation.dat))

### Solution
The file is a DOS/MBR boot sector.
```bash
$ file proclamation.dat
proclamation.dat: DOS/MBR boot sector
```

Running the file with qemu displays the following message:
<p align="center" markdown="1">
![ProclamationMessage](/assets/crowdstrikeCTF/proclamation-message.png)
</p>

#### Debugging with GDB
Next, I run  the following command so I can debug the bootloader with GDB.
```bash
$ qemu-system-x86_64 -s -S proclamation.dat
```
`-s`: is shorthand for `-gdb tcp::1234` which says to start a gdb server listening on TCP port 1234

`-S`: prevents the CPU from starting which gives GDB time to connect


Then I connect to the GDB server:
```bash
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

## Matrix
### Description
<p align="center" markdown="1">
![MatrixDescription](/assets/crowdstrikeCTF/matrix-description.png)
</p>

> With the help of your analysis, we got onto the trail of the group and found their hidden forum on the Deep Dark Web. Unfortunately, all messages are encrypted. While we believe that we have found their encryption tool, we are unsure how to decrypt these messages. Can you assist?

For this challenge, we're given two pieces of information an onion link (http://spacesftw5q4uyamog5qgcf75a5hbhmr2g24jqsdcpjnew63zkq7ueyd.onion/) and a Python encryption script (crypter.py).

#### Hidden Forum
Visiting the onion link with TOR brings us to a forum with encrypted messages.
<p align="center" markdown="1">
![HiddenForum](/assets/crowdstrikeCTF/hidden-forum.png)
</p>

```
Welcome on board!
259F8D014A44C2BE8FC573EAD944BA63 21BB02BE026D599AA43B7AE224E221CF 00098D47F8FFF3A7DBFF21376FF4EB79 B01B8877012536C10394DF7A943731F8 9117B49349E078809EA2EECE4AA86D84 4E94DF7A265574A379EB17E4E1905DB8 49280BD0040C23C98B05F160905DB849 280B6CB9DFECC6C09A0921314BD94ABF 3049280B5BFD8953CA73C8D1F6D0040C 1B967571354BAAB7992339507BBB59C6 5CDA5335A7D575C970F1C9D0040C23C9 8B08F78D3F40B198659B4CB137DEB437 08EB47FB978EF4EB7919BF3E97EA5F40 9F5CF66370141E345024AC7BB966AEDF 5F870F407BB9666F7C4DC85039CBD819 994515C4459F1B96750716906CB9DF34 5106F58B3448E12B87AFE754C0DD802C 41C25C7AAAFF7900B574FC6867EA35C5 BB4E51542C2D0B5645FB9DB1C6D12C8E F62524A12D5D5E622CD443E02515E7EB 991ACCC0C08CE8783F7E2BAD4B16D758 530C79003E5ED61DFE2BE70F50A6F9CA 288C

Let's fight back!
259F8D014A44C2BE8F7FA3BC3656CFB3 DF178DEA8313DBD33A8BAC2CD4432D66 3BC75139ECC6C0FFFBB38FB17F448C08 17BF508074D723AAA722D4239328C6B3 7F57C0A5249EA4E79B780DF081E997C0 6058F702E2BF9F50C4EC1B5966DF27EC 56149F253325CFE57A00B57494692921 94F383A3535024ACA7009088E70E6128 9BD30B2FCFE57A00B5749469292194F3 83A3533BAB08CA7FD9DC778386803149 280BE0895C0984C6DC77838C2085B10B 3ED0040C3759B05029F8085EDBE26DE3 DF25AA87CE0BBBD1169B780D1BCAA097 9A6412CCBE5B68BD2FB780C5DBA34137 C102DBE48D3F0AE471B77387E7FA8BEC 305671785D725930C3E1D05B8BD884C0 A5246EF0BF468E332E0E70009CCCB4C2 ED84137DB4C2EDE078807E1616AA9A7F 4055844821AB16F842

FLAGZ!
259F8D014A44C2BE8FC50A5A2C1EF0C1 3D7F2E0E70009CCCB4C2ED84137DB4C2 EDE078807E1616C266D5A15DC6DDB60E 4B7337E851E739A61EED83D2E06D6184 11DF61222EED83D2E06D612C8EB5294B CD4954E0855F4D71D0F06D05EE
```

It's safe to assume that we're looking to decrypt the "FLAGZ!" post to get the flag for this challenge.

#### crypter.py
```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
'''              ,
                /|      ,
   ,--.________/ /-----/|-------------------------------------.._
  (    /_/_/_/_  |--------- DEATH TO ALL TABS ---------------<  _`>
   `--´        \ \-----\|-------------------------------------''´
                \|      '
'''#             '
assert __name__ == '__main__'
import sys
def die(E):
    print(F'E:',E,file=sys.stderr)
    sys.exit(1)
T=lambda A,B,C,D,E,F,G,H,I:A*E*I+B*F*G+C*D*H-G*E*C-H*F*A-I*D*B&255
def U(K):
    R=pow(T(*K),-1,256)
    A,B,C,D,E,F,G,H,I=K
    return [R*V%256 for V in
     [E*I-F*H,C*H-B*I,B*F-C*E,F*G-D*I,A*I-C*G,C*D-A*F,D*H-E*G,B*G-A*H,A*E-B*D]]
def C(K,M):
    B=lambda A,B,C,D,E,F,G,H,I,X,Y,Z:bytes((A*X+B*Y+C*Z&0xFF,
        D*X+E*Y+F*Z&0xFF,G*X+H*Y+I*Z&0xFF))
    N=len(M)
    R=N%3
    R=R and 3-R
    M=M+R*B'\0'
    return B''.join(B(*K,*W) for W in zip(*[iter(M)]*3)).rstrip(B'\0')
len(sys.argv) == 3 or die('FOOL')
K=bytes(sys.argv[2], 'ascii')
len(K)==9 and T(*K)&1 or die('INVALID')
M=sys.stdin.read()
if sys.argv[1].upper() == 'E':
    M=B'SPACEARMY'+bytes(M,'ascii')
    print(C(U(K),M).hex().upper())
else:
    M=C(K,bytes.fromhex(M))
    M[:9]==B'SPACEARMY' or die('INVALID')
    print(M[9:].decode('ascii'))
```

The encryption script checks that it receives 2 arguments: 
- if the 1st argument is "E", it will encrypt input received from stdin. Anything else will attempt to decrypt.
- 2nd argument is a 9 byte key(K) that must satisfy the following: `T(*K)&1 == 1` where: `T=lambda A,B,C,D,E,F,G,H,I:A*E*I+B*F*G+C*D*H-G*E*C-H*F*A-I*D*B&255`


### Solution
From the `encrypter.py` script, we can see that the  first 9 bytes of the decrypted text must be "SPACEARMY". We can also see that in all encrypted messages, the first 9 bytes start with "259F8D014A44C2BE8F". So we can assume that "259F8D014A44C2BE8F" should decrypt to "SPACEARMY".

Using this information, I use [Z3](https://github.com/Z3Prover/z3) to solve for K that satisfies these constraints.

#### Solver Script
```python
from z3 import *

A = Int('A')
B = Int('B')
C = Int('C')
D = Int('D')
E = Int('E')
F = Int('F')
G = Int('G')
H = Int('H')
I = Int('I')

A,B,C,D,E,F,G,H,I,R,V = BitVecs('A B C D E F G H I R V', 32)

T=lambda A,B,C,D,E,F,G,H,I: A*E*I + B*F*G + C*D*H - G*E*C - H*F*A - I*D*B & 255

# Solves for K (U(K)) that results in 259F8D014A44C2BE8F 
"""
solve((A*83 + B*80 + C*65)&0xFF == 0x25,
	  (D*83 + E*80 + F*65)&0xFF  == 0x9F,
	  (G*83 + H*80 + I*65)&0xFF  == 0x8D,
	  (A*67 + B*69 + C*65)&0xFF  == 0x01,
	  (D*67 + E*69 + F*65)&0xFF  == 0x4A,
	  (G*67 + H*69 + I*65)&0xFF  == 0x44,
	  (A*82 + B*77 + C*89)&0xFF  == 0xC2,
	  (D*82 + E*77 + F*89)&0xFF  == 0xBE,
	  (G*82 + H*77 + I*89)&0xFF  == 0x8F)


# Solution
[A = 207,
 F = 139,
 E = 223,
 D = 76,
 I = 70,
 C = 72,
 H = 11,
 B = 28,
 G = 109]
"""

s = Solver()

# Solve for original K
s.add(A>=33, A<=126) # Printable char range
s.add(B>=33, B<=126)
s.add(C>=33, C<=126)
s.add(D>=33, D<=126)
s.add(E>=33, E<=126)
s.add(F>=33, F<=126)
s.add(G>=33, G<=126)
s.add(H>=33, H<=126)
s.add(I>=33, I<=126)
s.add(R*(E*I-F*H)%256 == 207)
s.add(R*(C*H-B*I)%256 == 28)
s.add(R*(B*F-C*E)%256 == 72)
s.add(R*(F*G-D*I)%256 == 76)
s.add(R*(A*I-C*G)%256 == 223)
s.add(R*(C*D-A*F)%256 == 139)
s.add(R*(D*H-E*G)%256 == 109)
s.add(R*(B*G-A*H)%256 == 11)
s.add(R*(A*E-B*D)%256 == 70)
s.check()
#print(s.model())
print(''.join([chr(x.as_signed_long()) for x in [s.model()[A],
      s.model()[B],
      s.model()[C],
      s.model()[D],
      s.model()[E],
      s.model()[F],
      s.model()[G],
      s.model()[H],
      s.model()[I]]]))
```
#### Output
`SP4evaCES`

The original key is "SP4evaCES"

#### Decrypting
Now that we have the original key, we can decrypt the "FLAGZ!" forum post:
```bash
$ echo "259F8D014A44C2BE8FC50A5A2C1EF0C1 3D7F2E0E70009CCCB4C2ED84137DB4C2 EDE078807E1616C266D5A15DC6DDB60E 4B7337E851E739A61EED83D2E06D6184 11DF61222EED83D2E06D612C8EB5294B CD4954E0855F4D71D0F06D05EE" | python3 crypter.py D SP4evaCES

Good job!

 040 == 32 == 0x20

CS{if_computers_could_think_would_they_like_spaces?}
```

Flag: `CS{if_computers_could_think_would_they_like_spaces?}`