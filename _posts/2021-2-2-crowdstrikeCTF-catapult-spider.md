---
layout: post
title: "Crowdstrike CTF: Catapult Spider Writeup"
permalink: /posts/crowdstrikeCTF-catapult-spider-writeup
category: CTF
tags: [osint]
---
Catapult Spider is a ransomware adversary.
> Rabid fans of the memetacular Doge and the associated crypto currency, CATAPULT SPIDER are trying to turn their obsession into a profit. Watch out for your cat pictures, lest CATAPULT SPIDER intrude your networks and extort them for Dogecoin.
<p align="center" markdown="1">
![CatapultSpider](/assets/crowdstrikeCTF/catapultspider.jpg)
</p>


## Much Sad
### Description
<p align="center" markdown="1">
![MuchSadDescription](/assets/crowdstrikeCTF/much-sad-description.png)
</p>

>We have received some information that CATAPULT SPIDER has encrypted a client's cat pictures and successfully extorted them for a ransom of 1337 Dogecoin. The client has provided the ransom note, is there any way for you to gather more information about the adversary's online presence?

For this challenge, we're given a ransom note called, `much_sad.txt`, the contents of the file is below.

```
+------------------------------------------------------------------------------+
|                                                                              |
|                        ,oc,                                                  |
|   BAD CAT.            ,OOxoo,                                  .cl::         |
|                       ,OOxood,                               .lxxdod,        |
|       VERY CRYPTO!    :OOxoooo.                             'ddddoc:c.       |
|                       :kkxooool.                          .cdddddc:::o.      |
|                       :kkdoooool;'                      ;dxdddoooc:::l;      |
|                       dkdooodddddddl:;,''...         .,odcldoc:::::ccc;      |
|                      .kxdxkkkkkxxdddddddxxdddddoolccldol:lol:::::::colc      |
|                     'dkkkkkkkkkddddoddddxkkkkkxdddooolc:coo::;'',::llld      |
|                 .:dkkkkOOOOOkkxddoooodddxkxkkkxddddoc:::oddl:,.';:looo:      |
|             ':okkkkkkkOO0000Okdooodddddxxxxdxxxxdddddoc:loc;...,codool       |
|           'dkOOOOOOkkkO00000Oxdooddxxkkkkkkxxdddxxxdxxxooc,..';:oddlo.       |
|          ,kOOO0OOkOOOOOO00OOxdooddxOOOOOkkkxxdddxxxxkxxkxolc;cloolclod.      |
|         .kOOOO0Okd:;,cokOOkxdddddxOO0OOOOOkxddddddxkxkkkkkxxdoooollloxk'     |
|         l00KKKK0xl,,.',xkkkkkxxxxkOOOkkOkkkkkxddddddxkkkkkkkkxoool::ldkO'    |
|        '00KXXKK0oo''..ckkkkkkkOkkkkkkxl;'.':oddddddxkkkkkkkkkkkdol::codkO.   |
|        xKKXXK00Oxl;:lxkkkkkkOOkkddoc,'lx:'   ;lddxkkkkkkkxkkkkkxdolclodkO.   |
|       ;KKXXXK0kOOOOOkkkkOOOOOOkkdoc'.'o,.  ..,oxkkkOOOkkkkkkkkkkddoooodxk    |
|       kKXKKKKKOOO00OOO00000OOOkkxddo:;;;'';:okOO0O0000OOOOOOOOOkkxddddddx    |
|      .KKKKKKKKOkxxdxkkkOOO000OkkkxkkkkkxxkkkkkOO0KKKKK0OOOO000OOOkkdddddk.   |
|      xKKKKKKc,''''''';lx00K000OOkkkOOOkkkkkkkkO0KKKKKK0OO0000O000Okkxdkkx    |
|     'KK0KKXx. ..    ...'xKKKK00OOOOO000000000OO0KKKKKKKKKKKKK0OOOOOkxdkko    |
|     xKKKKKXx,...      .,dKXKK00000000KKKKKKKKKKKKKKKKKKKK000OOOOOOkxddxd.    |
|    ,KKKKKXKd'.....  ..,ck00OOOOOOkO0KKKKKKKKKKKKKKKKKK0OOOOkkkkkkkxdddo.     |
|    .KKKKK0xc;,......',cok0O0OOOkkkk0KKKK00000KKK000OOOkkkkkkkkkkkxdddd.      |
|    .KKKKK0dc;,,'''''',:oodxkkkkkkkkkOOOOkOOOOkkkkkkkkkkkkkkkOOkkxdddd,       |
|     0KKKKK0x;'.   ...';lodxxkkkkkkddkkkkkkkkkkkkkkkkkkOOOOOkkOkkkxddc        |
|     xKKKKKK0l;'........';cdolc:;;;:lkkkkkkkkkkkkkkkkOO000OOOOOOkxddd.        |
|     :KKKKK00Oxo:,'',''''...,,,;;:ldxkkkkkkkkkkkkkOkkOOOOOOOOkkkxddd'         |
|      oKKKKK0OOkxlloloooolloooodddxkkkkkkkkkkkkkkkkkkkkkkkOOkkkxddd.          |
|       :KKK00OO0OOkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkkO0Okkkkkkkkxddd:            |
|        o0KK00000000OOkkkkkkkkkkkkkkkkkkkkkkkkkkO0000Okkkkkkxdo;.             |
|         'd00000000OOOOOOkkkkkkkkkkkkkkkkkOkOO00Okkkkkkkkkkko,                |
|           .oO00000OOOOOkkkkkkkkkkkkkkkkkkOOOOkOOkkkkkkkkko'                  |
|             .;xO0OOOOOOkkkkkkkkkkkkkkkkkkkkkOkkkkkkkkd:.                     |
|                .lxOOOOkkkkkkkkkkkkkkkkkkkxxxkkkkkd:'                         |
|                   .;okkkkkkkkxxkkdxxddxdxdolc;'..                            |
|                       ...',;::::::;;,'...                                    |
|                                                                              |
|                            MUCH SAD?                                         |
|                      1337 DOGE = 1337 DOGE                                   |
|                DKaHBkfEJKef6r3L1SmouZZcxgkDPPgAoE                            |
|              SUCH EMAIL shibegoodboi@protonmail.com                          |
+------------------------------------------------------------------------------+
```

### Solution
This challenge asks us to gather more information on the adversary's online presence, which tells me this is an OSINT challenge.

At the bottom of the ransom note is an email address, `shibegoodboi@protonmail.com`, so I search the username `shibegoodboi` online and see what I can find.

The first result on Google is a Twitter account: `https://twitter.com/shibegoodboi`

<p align="center" markdown="1">
![ShibeTwitter](/assets/crowdstrikeCTF/shibe-twitter.png)
</p>

In the profile, there is a link to a Github page: `https://github.com/shibefan`

<p align="center" markdown="1">
![ShibeGithub](/assets/crowdstrikeCTF/shibe-github.png)
</p>

Checking out the first repo (shibefan.github.io) and navigating through some of the files leads us to the flag in `index.html`:

<p align="center" markdown="1">
![ShibeIndex](/assets/crowdstrikeCTF/shibe-index.png)
</p>


Flag: `CS{shibe_good_boi_doge_to_the_moon}`