---
title: JerseyCTF IV Writeups
layout: post
date: 2024-03-25
permalink: jerseyctf-2024-writeups
category: ctf writeups
author: Inv1nc
---

![](/assets/images/24032501.png)

## OSINT

### this-is-not-the-flag-you-are-looking-for

#### Description : 

I received this note with this code. Enclosed with it, it asked for help finding a specific US Navy ship that was linked to this code. Can you decipher this code to give me the name of this US Navy ship and the type of ship it was with its associated number?  
Flag Format : `jctf{USS_Name_Of_Ship_TypeOfShip_Number}`  

![](/assets/images/24032502.png)

#### Solution : 
  - Quickly searched the image over the internet.
  - Found that image contain ```Flag Semaphore```
  - Decrypted the text:  `FIREPOWER FOR FREEDOM` using [Wikipedia](https://en.wikipedia.org/wiki/Flag_semaphore)  
**Flag : jctf{USS_New_Jersey_BB_62}** 

-------------

## Crypto

### Attn-Agents

### Description :

We heard a distress call and received a transmission. Only this text came through. You better take a look at this at once! It's definitely not Greek, I'll tell you that right now.  

#### Given Cipher : 

Dwwhqwlrq MFWI djhqwv! Dq xqnqrzq DSW lv klmdfnlqj qhwzrunv wr vsuhdg vwhdowk pdozduh xvlqj vwrohq vrxufh frgh. Brxu plvvlrq: wudfn grzq wkh vrxufh ri wkh ohdnv dqg vwrs wkh zlgh-vsuhdg dwwdfnv dfurvv rxu qhwzrunv. Wlph lv uxqqlqj rxw. Wkh {idwh-ri-wkh-zhe} lv lq brxu kdqgv!


#### Solution : 
 - Ceaser cipher with key 3
```shell
echo "{idwh-ri-wkh-zhe}" | tr 'a-z' 'x-za-w'
```
 
**Flag : jctf{fate-of-the-web}**

---

## Misc

### Internal Tensions

![](/assets/images/24032503.png)

#### Solution : 
  - Get Over to Internet Archive and searched the jerseyctf.com 

```shell
curl https://web.archive.org/web/20240215014427/https://jerseyctf.com/ -s | > grep jctf{.*} -o
```

**Flag : jctf{th3_1nt3rn3t_n3v3r_f0rg3t5_y0ur_b1und3r5}**
