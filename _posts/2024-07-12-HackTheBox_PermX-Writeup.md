---
layout: post
title: "HackTheBox: PermX"
description: A writeup for HackTheBox PermX
date:   2024-07-12 08:49 +0530
image:  '/images/post/permx.jpg'
tags:   [HackTheBox,htb]
---
## Setup

`mkdir -p HackTheBox/permx/scans; cd HackTheBox/permx`

IP Address: 10.10.11.23


`echo "10.10.11.23 permx.htb" | sudo tee -a /etc/hosts`
10.10.11.23 permx.htb

***
### Enumeration 

`nmap -sC -sV -Pn -p- permx.htb -oN scans/nmap -vv`
`nmap -sC -sV -p- -T5 --min-rate 2500 -oN scans/nmap_2 permx.htb`

```
# nmap findings
Scanning permx.htb (10.10.11.23) [65535 ports]
Discovered open port 22/tcp on 10.10.11.23
Discovered open port 80/tcp on 10.10.11.23

```

`gobuster dir --no-error -e -x php,html,txt -u http://permx.htb -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 100 --output scans/gobuster`


[Gobuster Cheatsheet](https://linuxcscom.wordpress.com/gobuster/)

```
# gobuster findings
http://permx.htb/.html                (Status: 403) [Size: 274]
http://permx.htb/index.html           (Status: 200) [Size: 36182]
http://permx.htb/.php                 (Status: 403) [Size: 274]
http://permx.htb/about.html           (Status: 200) [Size: 20542]
http://permx.htb/contact.html         (Status: 200) [Size: 14753]
http://permx.htb/img                  (Status: 301) [Size: 304] [--> http://permx.htb/img/]
http://permx.htb/css                  (Status: 301) [Size: 304] [--> http://permx.htb/css/]
http://permx.htb/courses.html         (Status: 200) [Size: 22993]
http://permx.htb/team.html            (Status: 200) [Size: 14806]
http://permx.htb/lib                  (Status: 301) [Size: 304] [--> http://permx.htb/lib/]
http://permx.htb/js                   (Status: 301) [Size: 303] [--> http://permx.htb/js/]
http://permx.htb/404.html             (Status: 200) [Size: 10428]
http://permx.htb/LICENSE.txt          (Status: 200) [Size: 1422]
http://permx.htb/testimonial.html     (Status: 200) [Size: 13018]

```

`ffuf -r -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-110000.txt:ff -u http://permx.htb/ -H 'Host: ff.permx.htb' -o scans/ffuf_subdomains`

```
#FFUF subdomain search findings 
lms                     [Status: 200, Size: 19347, Words: 4910, Lines: 353, Duration: 326ms]

```

![662046b6be221a7188a1a3b8039ed228.png]({{site.baseurl}}/images/post/662046b6be221a7188a1a3b8039ed228.png)
![9979f97ca52527b0e2027d77314870b7.png]({{site.baseurl}}/images/post/9979f97ca52527b0e2027d77314870b7.png)	

Got Shell 

![0b987dc62985c66547d4dc56c1339339.png]({{site.baseurl}}/images/post/0b987dc62985c66547d4dc56c1339339.png)

![d6332429bc4aec055350627214743639.png]({{site.baseurl}}/images/post/d6332429bc4aec055350627214743639.png)
![8be1d7b8f05a1822b5706aac91cc0faa.png]({{site.baseurl}}/images/post/8be1d7b8f05a1822b5706aac91cc0faa.png)

/var/www/chamilo/app/config/configuration.php

```
$_configuration['db_host'] = 'localhost';
$_configuration['db_port'] = '3306';
$_configuration['main_database'] = 'chamilo';
$_configuration['db_user'] = 'chamilo';
$_configuration['db_password'] = '03F6lY3uXAP2bkW8';
```

***
# user flag
Location: /home/mtz | Flag: ************************



***
#### Privilege Escalation


![d036092b845aa2c3a7630726b14c9b0d.png]({{site.baseurl}}/images/post/d036092b845aa2c3a7630726b14c9b0d.png)
![8864d67700598ccf0af454b9a5895344.png]({{site.baseurl}}/images/post/8864d67700598ccf0af454b9a5895344.png)

in this change NOPASSWD for mtz user to all and do sudo su 


# root flag
Location: /root/root.txt | Flag:************************ 
```