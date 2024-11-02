---
description: TryHackme Simple CTF writeup by Thamizhiniyan C S
---

# Simple CTF

## Overview

Greetings everyone,

In this write-up, we will tackle Simple CTF from TryHackMe.

Machine link: [Simple CTF](https://tryhackme.com/room/easyctf)

Difficulty Level: Easy

Let's Begin 🙌

Firstly, connect to the THM server using the OpenVPN configuration file generated by THM. [Click Here](https://tryhackme.com/r/access) to learn more about how to connect to VPN and access the boxes.

Once connected to the VPN service, click on "Start Machine" to access the machine's IP.

Upon joining the machine, you will be able to view the IP address of the target machine.

***

First start the machine and run a standard Nmap scan on the target.

Command: `nmap -A -T4 -v <Target_IP>`

<figure><img src="../../.gitbook/assets/Untitled (2).png" alt=""><figcaption></figcaption></figure>

From the output we can see that there are a total of 3 services running on the target machine:

| PORT | SERVICE | VERSION             |
| ---- | ------- | ------------------- |
| 21   | FTP     | vsftpd 3.0.3        |
| 80   | HTTP    | Apache httpd 2.4.18 |
| 2222 | SSH     | Open SSH 7.2p2      |

Now, First I visited the Apache server hosted on port 80.

<figure><img src="../../.gitbook/assets/Untitled 1 (2).png" alt=""><figcaption></figcaption></figure>

It displayed the default apache welcome page. So next I tried directory enumeration on this web server using gobuster.

Command: `gobuster dir -u http://<TARGET_IP>/ -w **/usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt**`

<figure><img src="../../.gitbook/assets/Untitled 2 (2).png" alt=""><figcaption></figcaption></figure>

From the output of the gobuster, we can see that there is `/simple` directory.

Now visit the `http://<Target_IP>/simple` location.

<figure><img src="../../.gitbook/assets/Untitled 3 (2).png" alt=""><figcaption></figcaption></figure>

This web server hosts a CMS using the CMS Made Simple, which is open source CMS. On scrolling down to the end of the page we can find the version of the CMS in the footer section.

<figure><img src="../../.gitbook/assets/Untitled 4 (2).png" alt=""><figcaption></figcaption></figure>

The CMS version is `2.2.8` , I searched google, looking for exploits for this version of CMS and I got the following:

<figure><img src="../../.gitbook/assets/Untitled 5 (2).png" alt=""><figcaption></figcaption></figure>

This CMS is vulnerable to the exploit with the CVE `CVE-2019-9053` , which is an unauthenticated SQL Injection on this CMS.

I downloaded the exploit.

After downloading the exploit I tried to run the exploit using the following command:

`python2 <exploit.py> -u http://<targetip>/simple`

But it thrown me a error that the exploit requires a package `termcolor` to run. So I downloaded the package using the following command: `python2 -m pip install termcolor`

Then I tried to run the exploit, the exploit ran successfully and the following output was obtained:

<figure><img src="../../.gitbook/assets/Untitled 6 (2).png" alt=""><figcaption></figcaption></figure>

From this we have found the username is `mitch` and we have also got the password salt and hash. Now we can try to crack this password hash using `hashcat`.

Command: `hashcat -m 20 0c01f4468bd75d7a84c7eb73846e8d96:1dac0d92e9fa6bb2 **/usr/share/wordlists/rockyou.txt**`

<figure><img src="../../.gitbook/assets/Untitled 7 (2).png" alt=""><figcaption></figcaption></figure>

And we have cracked the password the password is `secret`

Now we can login via ssh using the obtained credentials:

username: `mitch`

password: `secret`

Command: `ssh mitch@<Target_IP> -p 2222`

<figure><img src="../../.gitbook/assets/Untitled 8 (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Untitled 9 (2).png" alt=""><figcaption></figcaption></figure>

And now we got the user flag.

Now we have escalate our privilege to access the root flag.

On Further enumeration, I have found another user in the home directory named `sunbath`.

I was looking out for privilege escalation vectors. I tried the `sudo -l` command, to check whether the user `mitch` can run any application with root privileges.

<figure><img src="../../.gitbook/assets/Untitled 10 (2).png" alt=""><figcaption></figcaption></figure>

From the output, we can devise that the user `mitch` can run `vim` with root privileges.

On checking `gtfobins` \[ [https://gtfobins.github.io/gtfobins/vim/#sudo](https://gtfobins.github.io/gtfobins/vim/#sudo) ], we can use the following command to get a shell with root privilege:

<figure><img src="../../.gitbook/assets/Untitled 11 (2).png" alt=""><figcaption></figcaption></figure>

And we got root access.

<figure><img src="../../.gitbook/assets/Untitled 12 (2).png" alt=""><figcaption></figcaption></figure>

And finally we got the root flag:

<figure><img src="../../.gitbook/assets/Untitled 13 (1).png" alt=""><figcaption></figcaption></figure>

The answers for all the Task questions:

<figure><img src="../../.gitbook/assets/Untitled 14 (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Untitled 15 (1).png" alt=""><figcaption></figcaption></figure>

Thank You ....