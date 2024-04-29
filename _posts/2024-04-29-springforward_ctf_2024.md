---
title: SpringForwardCTF 2024 Writeup
layout: post
date: 2024-04-29
permalink: springforwardctf-2024-writeup
category: ctf writeups
description: few ctf challenges from SpringForward ctf 2024. 
author: Inv1nc
---

![](assets/images/2024/24042901.png)

Hello Friend,  

I solved few challenges in the SpringForward CTF 2024 which went down from April 26-28.  

Right now, I'm playing CTF's solo. I hope these writeups will open up a lot more doors.  

---
## Web

### Socratic Script

Given a text file(`ancient_tome.txt`) that contains `grant_me_passage_almighty_zeus`.  

In order to get the flag we have to upload the `ancient_tome.txt`  

The below script prventing us from uploading the text file.  

```js
//Participants will need to remove or disable this script to allow uploads
document.getElementById('uploadForm').addEventListener('submit', function(event) {
	event.preventDefault();
	alert('Denied!');
});
```

After inspecting the upload button. we can find the `event listener`, clicking `REMOVE` button can disable the above script.  

![](assets/images/2024/24042902.png)

**flag: nicc{p@SSAGe_GR@Nt3D}**

### Into the Gorgon's Den

```javascript
function mirror() {
	var input = document.getElementById("mirrorInput").value;
	var output = document.getElementById("mirrorOutput");

	if (input.toLowerCase() === "suesrep") {
		var clue = getMirrorClue();
		output.innerHTML = clue;
		output.style.display = "block";
	}

	else {
		output.innerHTML = input.split("").reverse().join("");
		output.style.display = "none";
	}
}

```
From above script we can understand that input must be equal to `suesrep`, inorder to execute `getMirrorClue()` function.  

![](assets/images/2024/24042903.png)

Medusa's weakness is her reflection! she will turn her gaze at the 30th minute minus 2. The meaning of the clue is at the 28th minute of any hour, a clue pop up from a file called `time.php`.

```javascript
function revealClue(clue) {
	document.getElementById('petrifiedContent').textContent = clue;
	}

	function checkAvailability() {
		var xhttp = new XMLHttpRequest();

		xhttp.onreadystatechange = function () {
		if (this.readyState == 4 && this.status == 200) {
			document.getElementById('petrifiedContent').innerHTML = this.responseText;
		}
	};

	xhttp.open('GET', 'time.php', true);
	xhttp.send();
}
```

As My time is IST `(5:30 + 28) % (60 min) == 58 min`.

For me the `time.php` only showed the flag on `any hour : 58 min`.

![](assets/images/2024/24042904.png)


part1 is `sl4y`.  

**Part 2**

![](assets/images/2024/24042905.png)

cipher text : Gur frpbaq cneg bs gur synt vf gur ohg gur r vf n 3 !  

The above cipher text is the ceaser cipher with key 13.  

plain text : The second part of the flag is the but the e is a 3 !  

part2 is `th3`.  

**Part 3**


```javascript
function checkInput() {
	var input = document.getElementById('flagInput').value;
	var secretPhrase = 'sl4yth3';

	if (input === secretPhrase) {
		fetchFlag();
	} else {
		alert('Incorrect input. Please try again.');
	}
}
```

from above javascript code we will understood that input must be equal `sl4th3` to get the flag.

![](assets/images/2024/24042906.png)

part3 is `nicc{part1_part2_g0rg0n}`

**flag: nicc{sl4y_th3_g0rg0n}**

---

## Crypto

### Party at the Gardens

we are give with a image that contains cup. there are some cracks on the cup that look like Morse code

```
.-- .. -. . - .. -- .
```

![](assets/images/2024/24042907.png)

**flag : nicc{WINETIME}**

---

## OSINT

### Freezing February

The flag format is nicc{FirstName_LastName_SculptureName}

we are given with below image

![](assets/images/2024/24042908.jpg)

[click here](https://www.timeout.com/newyork/art/ice-sculpture-in-times-square) for more information about above image.

**flag : nicc{Lovie_Pignata_Smitten}**

### Its a Bull With What

*Description* : It has been said that the mighty Minotaur is found in the Aegean Sea on some palace. Can you help me find the island name and the palace name, so that I can slay it?  

The flag format is nicc{Island_Name_Palace_Name}  

![](assets/images/2024/24042909.png)

To Know more about Minotaur [click here](https://en.wikipedia.org/wiki/Minotaur).

**flag : nicc{Crete_Palace_of_Minos}**

---

## Misc

### Horsing Around at Troy

The hint suggests that there might be something hidden inside the provided image.  

By running `binwalk`, we can extract some hidden content.  

```shell
binwalk -e totally-innocent-horse.jpg
cd _totally-innocent-horse.jpg.extracted
```

![](assets/images/2024/24042910.png)

**flag : nicc{7Ro14-H1pPo2}**

### Minerva's Quest

This challenge focused on answering multiple-choice questions related to infosec. Participants were directed to Google the questions and learn about them.  

After answering 13 questions from given [google form](https://forms.gle/MvwyPcchuTSt1Mvs8). we can get flag.

![](assets/images/2024/24042911.png)

**flag : NICC{_Minerva's_Blessing_for_U}**

---

## Forensics

### Browsing-History

Given a `json` file that contain's browser history.

```shell
cat zeus-data.json  | grep -o 'nicc{[^}]*}'
```

**flag : nicc{jup1t3R-15-4W350M3}**

---

Thanks for reading!

