# Writeup Bandit wargame (OverTheWire)

OverTheWire ([link to the webpage](https://overthewire.org/)) is a website which contains a lot of different levels in order to improve some 
security concepts, in the form of CTFs.

Particularly, in this blog we are going through the 'Bandit' wargame, which is for beginners and helps to better understand, in general, how the Linux systems work.

Let's go.

## Level 0 / 0 &rarr; 1
In this level we need to login through the *ssh* command. *ssh* stands for 'Secure Shell' and, according to its man page, *is a program for logging into a remote machine and for executing commands on a remote machine.*
In general, in order to access a remote machine, we need a **username** and a **password**. Here in this level, *bandit0* is both the username and the password.  
Note that, in the level description, they state that we need to connect through port 2220 (the default one is port 22).

So, in order to log into the machine we need to type the following command:  
```
  ssh bandit0@bandit.labs.overthewire.org -p 2220
```  
and then insert the password (i.e., bandit0).  
Once inside, we list the files in the home directory and see we have a *readme* file, as we can observe in the figure below. 
![ls files in home directory](/images/bandit0/ls.png?raw=true)  
As we can observe, the owner of the file is *bandit1* (who has *read* -r- and write -w- permissions), while the group-owner is *bandit0* (i.e., us) and has read permission.  
Therefore, simply read the content of the readme file in order to get the password for the next level, by launching the command:  
```
  cat readme
```


## Level 1 &rarr; 2
It is very similar to the previous level, except that the password for level 2 is contained in a file named "**-**". Since the *cat* command treats the dash symbol as stdin ([link](https://unix.stackexchange.com/questions/16357/usage-of-dash-in-place-of-a-filename)), in order to read such a file we need to use the following syntax:  
```
  cat ./-
```


## Level 2 &rarr; 3
Here, the filename contains spaces, so in order to cat such file we can do it in two possible ways:
* inserting the filename inside double quotes;  
* escaping space characters with backslash.

Digging deeper on how the cat command works with filanames which contain spaces, we can observe its behaviour using the *strace* command.
Indeed, as we can see in the figure below, if we use the following command:

```
strace cat spaces in this filename 
```

there are as many calls to *cat* as the number of separated-by-space items (in the image, three). 
![img3](/images/bandit0/img3.png?raw=true) 

Instead, using:

```
cat "spaces in this filename"
```

there is only one call to *cat*.

![img4](/images/bandit0/img4.png?raw=true)

So, the possible ways to get the flag:

![img2](/images/bandit0/img2.png?raw=true)


## Level 3 &rarr; 4
In the Linux systems, if we want to list files in a directory, we can simply run the *ls* command. However, if the name of a file starts with a dot (for instance, .s3cret), this file is a hidden one. Therefore, in order to "see" such file we need to add the *-a* option to the ls command.

Solution:

![img5](/images/bandit0/img5.png?raw=true)


## Level 4 &rarr; 5
In the home directory there is an *inhere* directory. Using what we learnt in level 1 &rarr; 2 and the following command:

```
file ./-file0*
```

we obtain information for each file in the directory, whose name start with "-file0".

![img6](/images/bandit0/img6.png?raw=true)

As we can observe from the output, *-file07* is an ASCII file, which contains the password for the next level.


## Level 5 &rarr; 6
As in the level before, we need to find a file which contains the password of the next level, and resides in the ~/inhere/ directory. 

This file has the following properties:
1) human-readable
2) 1033 bytes in size
3) not executable

If we try to list the files in *inhere* we find a lot of directories, each containing files. So, we cannot find it manually.

The command for solving the problem is **find**, with the following options:
* *-type*, which sets the type of the file;
* *-readable*, which, as the name suggests,  matches files which are readable;
* *-executable*, which matches files which are executable, but since the file is NOT executable, we will prepend it with **!**;
* *-size*, which looks for a file with *n* units of space.

If you want to know more, follow this [link](https://www.explainshell.com/explain?cmd=find+-type+f+-readable+%21+-executable+-size+1033c)

Then, the final command for solving this level is the following:

```
cd inhere
a=`find ./ -type f -readable ! -executable -size 1033c` ; cat $a;
```

![img7](/images/bandit0/image7.png)


## Level 6 &rarr; 7
Very similar to previous level, but with file with different features:
1) owned by user bandit7;
2) owned by group bandit6;
3) 33 bytes in size

Solution:

```
find / -size 33c -group bandit6 -user bandit7 2> /dev/null
```

Note: **2> /dev/null** is useful for sending all the *trash* output to /dev/null. This is due to the fact that in some directories we have no permission for listing files.

For more about the command used: [link](https://www.explainshell.com/explain?cmd=find++-size+33c+-group+bandit6+-user+bandit7)


## Level 7 &rarr; 8
Since only one *millionth* word is present in data.txt file, we simply have to run the following command in order to get the password:

```
cat data.txt | grep millionth
```
