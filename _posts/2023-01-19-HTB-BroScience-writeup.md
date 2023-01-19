# Writeup of the BroScience machine (HackTheBox)

![logo_machine](/images/htb_broscience/logo_machine.png?raw=true)


The following machine is a medium-level one of linux-type.

Let's begin.

## Port scanning

We start by scanning the open ports with *nmap* and we get the following results:

![nmap_results](/images/htb_broscience/nmap.png?raw=true)

Here we note that:
  * 3 ports are open (ssh, http and https)
  * the hostname is **broscience.htb**


First of all, we add the entry IP-hostname to the */etc/hosts* file with the following command:

<code>sudo echo "<IP_machine> broscience.htb" >> /etc/hosts  
</code>
  
## Website
 
