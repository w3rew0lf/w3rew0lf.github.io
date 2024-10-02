---
layout: post
title: "HackTheBox: SolarLab - Writeup 13/07/2024"
description: A writeup for Hackthebox SolarLab
date:   2024-07-13 12:06:31 +0000
image:  '/images/post/solarlab.jpg'
tags:   [Hackthebox]
---
## Setup
`mkdir -p HackTheBox/solarlab/scans; cd HackTheBox/solarlab`
IP Address: 10.10.11.16


`echo "10.10.11.16 solarlab.htb" | sudo tee -a /etc/hosts`
10.10.11.16 solarlab.htb

***
### Enumeration 

`nmap -sC -sV -Pn -p- solarlab.htb -oN scans/nmap -vv`
`nmap -sC -sV -p- -T5 --min-rate 2500 -oN scans/nmap_2 solarlab.htb`
```
# nmap findings
PORT     STATE SERVICE       REASON  VERSION
80/tcp   open  http          syn-ack nginx 1.24.0
| http-methods: 
|_  Supported Methods: GET HEAD
|_http-title: SolarLab Instant Messenger
|_http-server-header: nginx/1.24.0
135/tcp  open  msrpc         syn-ack Microsoft Windows RPC
139/tcp  open  netbios-ssn   syn-ack Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds? syn-ack
6791/tcp open  http          syn-ack nginx 1.24.0
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: nginx/1.24.0
|_http-title: Did not follow redirect to http://report.solarlab.htb:6791/
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

Enumerating SMB found the following 
![e6fc6ff03d3213f4a8e8d99544670a7d.png]({{site.baseurl}}/images/post/e6fc6ff03d3213f4a8e8d99544670a7d.png)

![632231b54bead573cfacb44bdacafca2.png]({{site.baseurl}}/images/post/632231b54bead573cfacb44bdacafca2.png)
We know on Port 6791 a Subdomain exist report
A login is present using hydra and burpsuite we come to know the following creds

`BlakeB:ThisCanB3typedeasily1@`

using the login we loggin to the portal and find a vulnerable reporthub site. We find CVE-2023–33733 upon exploiting 
![b8fe0b990f622ff853d3590c165b0bfa.png]({{site.baseurl}}/images/post/b8fe0b990f622ff853d3590c165b0bfa.png)
![3e61feb0a23b50c5dffb3f7382ac3466.png]({{site.baseurl}}/images/post/3e61feb0a23b50c5dffb3f7382ac3466.png)
![e04566c33a6723e1c418c3e488cd5c8d.png]({{site.baseurl}}/images/post/e04566c33a6723e1c418c3e488cd5c8d.png)
![204e21c00604a5e6cf844851497e4121.png]({{site.baseurl}}/images/post/204e21c00604a5e6cf844851497e4121.png)
```
alexanderk:HotP!fireguard
claudias:007poiuytrewq
blakeb:ThisCanB3typedeasily1@

```
***

# user flag
Location: C:\Users\blake\Desktop  | Flag: cfeeadb3261b3507fddf84055ec296c2



# root flag
Location:C:\Users\Administrator\Desktop\root.txt  | Flag: e231949ecf562f5c172e560917188114

