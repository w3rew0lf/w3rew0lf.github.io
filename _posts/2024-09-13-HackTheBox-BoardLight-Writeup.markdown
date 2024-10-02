---
layout: post
title: "HackTheBox: BoardLight - Writeup 13/09/2024"
description: A writeup for Hackthebox BoardLight
date:   2024-09-13 12:06:31 +0000
image:  '/images/post/boardlight.jpg'
tags:   [Hackthebox,Linux]
---

## Setup
`mkdir -p ~/ctf/HackTheBox/boardlight/scans; cd ~/ctf/HackTheBox/boardlight`
IP Address: 10.10.11.11


`echo "10.10.11.11 boardlight.htb" | sudo tee -a /etc/hosts`
10.10.11.11 boardlight.htb

***
### Enumeration 

`nmap -sC -sV -Pn -p- boardlight.htb -oN scans/nmap -vv`
`nmap -sC -sV -p- -T5 --min-rate 2500 -oN scans/nmap_2 boardlight.htb`
![d911ed57aa529e692a9b0285f131216f.png]({{site.baseurl}}/images/post/d911ed57aa529e692a9b0285f131216f.png)
```
# nmap findings

```

boardlight.htb didnt have any directory or subdomain so found this on home page :
`board.htb`

![08f1bd4dbdf75820fcca97c443eb06dc.png]({{site.baseurl}}/images/post/08f1bd4dbdf75820fcca97c443eb06dc.png)
`ffuf -r -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-110000.txt:ff -u http://board.htb/ -H 'Host: ff.board.htb' -o scans/ffuf_subdomains`
```
#FFUF subdomain search findings 

```
![76e1952c31a82acd698b53ee35a2b806.png]({{site.baseurl}}/images/post/76e1952c31a82acd698b53ee35a2b806.png)

Homepage:
![43edaa7963024616cbda1f4eb734a86c.png]({{site.baseurl}}/images/post/43edaa7963024616cbda1f4eb734a86c.png)

Version was mentioned found a cve and exploit CVE-2023-30253

using hit and trial admin:admin worked

got reverse shell
![5fababf7e0c9ecd886fad188a1c599a6.png]({{site.baseurl}}/images/post/5fababf7e0c9ecd886fad188a1c599a6.png)
![b3a33e325de81cdf1e075579b6049593.png]({{site.baseurl}}/images/post/b3a33e325de81cdf1e075579b6049593.png)

using `find . -name conf*`
![7deb34c7ec3cfe17c2bd10a5350dc253.png]({{site.baseurl}}/images/post/7deb34c7ec3cfe17c2bd10a5350dc253.png)

after logging in larissa account and running linpeas we found 
![c97ba3c7c0203bf8ca7b1ca3af7f7ec2.png]({{site.baseurl}}/images/post/c97ba3c7c0203bf8ca7b1ca3af7f7ec2.png)

quick google search returned a cve CVE-2022-37706
running this script in machine gave us root 
![c9944044a84f44e5fa7a6a50bb2d0c4a.png]({{site.baseurl}}/images/post/c9944044a84f44e5fa7a6a50bb2d0c4a.png)

***
# user flag
Location:  /home/larissa | Flag: ad4c5ed4fc3e429c8a2346d0d336d5d9

after logging in larissa account and running linpeas we found 
![c97ba3c7c0203bf8ca7b1ca3af7f7ec2.png]({{site.baseurl}}/images/post/c97ba3c7c0203bf8ca7b1ca3af7f7ec2.png)
quick google search returned a cve CVE-2022-37706

running this script in machine gave us root 
![c9944044a84f44e5fa7a6a50bb2d0c4a.png]({{site.baseurl}}/images/post/c9944044a84f44e5fa7a6a50bb2d0c4a.png)

# root flag
Location:  /root | Flag: a875c049278b4b844fc9ffebd3cf5e47
