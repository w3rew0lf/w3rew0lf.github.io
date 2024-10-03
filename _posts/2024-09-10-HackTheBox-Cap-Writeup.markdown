---
layout: post
title: "HackTheBox: Cap - Writeup 10/09/2024"
description: A writeup for Hackthebox BoardLight
date:   2024-09-10 12:06:31 +0000
image:  '/images/post/cap.jpg'
tags:   [Hackthebox,Linux]
---
## Setup
`mkdir -p ~/ctf/HackTheBox/cap/scans; cd ~/ctf/HackTheBox/cap`
IP Address: 10.10.10.245


`echo "10.10.10.245 cap.htb" | sudo tee -a /etc/hosts`
10.10.10.245 cap.htb
***
### Enumeration 

`nmap -sC -sV -Pn -p- cap.htb -oN scans/nmap -vv`
`nmap -sC -sV -p- -T5 --min-rate 2500 -oN scans/nmap_2 cap.htb`
```
# nmap findings

```
![5e4d6a257d6a35f6a0145448052e4945.png]({{site.baseurl}}/images/post/5e4d6a257d6a35f6a0145448052e4945.png)

# user flag
When Navigating to home page `http://cap.htb/` it would show as follows:
![2723cb1eccbeb176ef5cc5e6aee5fe8c.png]({{site.baseurl}}/images/post/2723cb1eccbeb176ef5cc5e6aee5fe8c.png)

When Capturing the request for Security Snapshot (5 Second PCAP + Analysis): 

It would show as follows:
![ccca69b13e6f6b01bd49cf09938d799f.png]({{site.baseurl}}/images/post/ccca69b13e6f6b01bd49cf09938d799f.png)

Notice the one parameter when changed to 0 would show a new traffic capture and on opening pcap file we found sensitive data:
![610ec926473b027b1031dcc0915aeb3d.png]({{site.baseurl}}/images/post/610ec926473b027b1031dcc0915aeb3d.png)

Credentials found for FTP & ssh: 
Username:nathan 
Password:Buck3tH4TF0RM3!

logging in ftp using these creds


Location: ftp | Flag: de1##################################



![e71046f71004625aa6fae897043a1a9b.png]({{site.baseurl}}/images/post/e71046f71004625aa6fae897043a1a9b.png)
![17a2125c8e04b684086d6b6b34b4d320.png]({{site.baseurl}}/images/post/17a2125c8e04b684086d6b6b34b4d320.png)


# root flag
Location: /root | Flag: 7a3##################################
