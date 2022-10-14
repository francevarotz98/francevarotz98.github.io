# Walkthrough of the Bandit wargame (OverTheWire)

OverTheWire ([link to the webpage](https://overthewire.org/)) is a website which contains a lot of different levels in order to improve some 
security concepts, in the form of CTFs.

Particularly, in this blog we are going through the 'Bandit' wargame, which is for beginners and helps 
to better understand, in general, how the Linux systems work.

Let's go.

## Level 0 / 0 &rarr; 1
In this level we need to login through the *ssh* command. *ssh* stands for 'Secure Shell' and, according to its man page, *is a program for logging into a remote machine and for executing commands on a remote machine.*
In general, in order to access a remote machine, we need a **username** and a **password**. Here in this level, *bandit0* is both the username and the password.  
Note that, in the level description, they state that we need to connect through port 2220 (the default one is port 22).

So, in order to log into the machine we need to type the following command:  
<code>
  ssh bandit0@bandit.labs.overthewire.org -p 2220
</code>  
and then insert the password (i.e., bandit0).  
Once inside, we list the files in the home directory and see we have a *readme* file, as we can observe in the figure below. 
![ls files in home directory](/images/bandit0/ls.png?raw=true)  
As we can observe, the owner of the file is *bandit1* (who has *read* -r- and write -w- permissions), while the group-owner is *bandit0* (i.e., us) and has read permission.  
Therefore, simply read the content of the readme file in order to get the password for the next level, by launching the command:  
<code>
  cat readme
</code>


## Level 1 &rarr; 2
It is very similar to the previous level, except that the password for level 2 is contained in a file named "**-**". Since the *cat* command treats the dash symbol as stdin ([link](https://unix.stackexchange.com/questions/16357/usage-of-dash-in-place-of-a-filename)), in order to read such a file we need to use the following syntax:  
<code>
  cat ./-
</code>


## Level 2 &rarr; 3
**TODO**
  


