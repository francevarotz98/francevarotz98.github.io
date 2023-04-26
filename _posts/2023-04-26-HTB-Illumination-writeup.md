# Writeup Illumination challenge (HackTheBox)

The Illumination challenge belongs to the *Forensics* category and it is classified as (very) easy.

The aim of the challenge is to discover a secret token.

Let's begin.

---

## Files analysis

When we dowload and unzip the challenge file, we can see that three files are present: 

* bot.js
* config.json
* .git/

The *config.json* file contains five properties, and the one we are interested in, i.e., *token*, has the following value:"Replace me 
with token when in use! Security Risk!". So, the token value isn't present in this JSON.

Inspecintg *bot.js* we cannot find anything relevant.

---

## Jumping into the .git folder

Now, let's see if we can come up with useful information in the .git/ folder.

Running ```git status``` we note that we are in the master branch and that both bot.js and config.json have been modified.

With ```git log``` we get the following output:

![img1](/images/illumination/img1.png?raw=true)

So, it seems in the commit starting with *47241a*, the developer removed the token value from the code.

Using ```git show <commit_number>``` the base64ed value of the token is showed 

![img2](/images/illumination/img2.png?raw=true)

and the challenge is concluded :)
