---
title: HeroCTF Writeups
tags: forensics memory steganography ssrf web
---

![](/img/heroctf/hero.jpeg)

Howdy, we recently took part in HeroCTF V3 and we settled for #68 from 645 teams
here are some of my writeups

# Forensics

## We need you 1/5

##### Category

##### Forensics

## Description

Interpol and the FBI have been investigating for over a year now. They are trying to get their hands on two hackers very well known for their ransomware and their ultra efficient botnet.

After long months of investigation, they managed to get their hands on one of their servers. But, when they got it back the PC caught fire because of a defense mechanism set up by the two hackers.

The hard drive could not be saved, but they had time to put the RAM in liquid nitrogen and analyze it later.

You know what you have to do!

For this first step, find the name of the PC!

Author: Worty
Format: Hero{Name}


## Initial analysis.

- Extracting the archive gives us a 2.1GB memory dump,quick analysis at it turns out to be a windows memory forensics challenge


## Solution

- First things first with all memory forensics challenges is to determine the profile to use 
using the `imageinfo` volatility plugin 

```volatility -f capture.mem imageinfo```

resulting in 

```

|─[user@parrot]─[~/Desktop/CTFs/heroctf]
└──╼ $volatility -f capture.mem imageinfo 
Volatility Foundation Volatility Framework 2.6
INFO    : volatility.debug    : Determining profile based on KDBG search...
          Suggested Profile(s) : Win7SP1x86_23418, Win7SP0x86, Win7SP1x86
                     AS Layer1 : IA32PagedMemoryPae (Kernel AS)
                     AS Layer2 : FileAddressSpace (/home/skoki/Desktop/CTFs/heroctf/capture.mem)
                      PAE type : PAE
                           DTB : 0x185000L
                          KDBG : 0x82780c28L
          Number of Processors : 1
     Image Type (Service Pack) : 1
                KPCR for CPU 0 : 0x82781c00L
             KUSER_SHARED_DATA : 0xffdf0000L
           Image date and time : 2021-04-19 17:30:00 UTC+0000
     Image local date and time : 2021-04-19 19:30:00 +0200

```


using our profile as `Win7SP0x86`

we can get the PC name by using volatility plugin `envars`

```
┌─[user@parrot]─[~/Desktop/CTFs/heroctf]
└──╼ $volatility -f capture.mem --profile Win7SP0x86 envars | grep COMPUTER
Volatility Foundation Volatility Framework 2.6
     408 wininit.exe          0x0028f658 COMPUTERNAME                   KANNIBAL
     472 winlogon.exe         0x00082cd8 COMPUTERNAME                   KANNIBAL
     492 services.exe         0x002b07f0 COMPUTERNAME                   KANNIBAL
     500 lsass.exe            0x001007f0 COMPUTERNAME                   KANNIBAL
     508 lsm.exe              0x000807f0 COMPUTERNAME                   KANNIBAL
     628 svchost.exe          0x002907f0 COMPUTERNAME                   KANNIBAL
     692 VBoxService.ex       0x002707f0 COMPUTERNAME                   KANNIBAL
     748 svchost.exe          0x003007f0 COMPUTERNAME                   KANNIBAL
     800 svchost.exe          0x001b07f0 COMPUTERNAME                   KANNIBAL
     932 svchost.exe          0x003707f0 COMPUTERNAME                   KANNIBAL
     976 svchost.exe          0x000d07f0 COMPUTERNAME                   KANNIBAL
```
and we get the first flag which was the computer name

> Flag : Hero{KANNIBAL}

## We need you 2/5

#### Category

#### Forensic

### Description

It must be their team name.

For this second step, find the user's name and password in clear text.

Author: Worty

Format: Hero{Username:Password}

### Solution 

- still using the same dump provided we can start hunting for users and their credentials

using volatility plugin `hashdump` we get a list of users and their NT hashes

```
┌─[✗]─[user@parrot]─[~/Desktop/CTFs/heroctf]
└──╼ $volatility -f capture.mem --profile Win7SP0x86 hashdump
Volatility Foundation Volatility Framework 2.6
Administrateur:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Invit:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Razex:1000:aad3b435b51404eeaad3b435b51404ee:78d9c7e905c695087ee3baa755ce43e4:::
```

users being `Administrateur` with hash `31d6cfe0d16ae931b73c59d7e0c089c0`
same hash with user `Invit` but i couldn't crack the hash 

but user `Razex` with hash as `78d9c7e905c695087ee3baa755ce43e4` 
which we can use [hashes.com](https://hashes.com) 

![](/img/heroctf/hashes.png)

and we get the password as `liverpoolfc123` of type `NTLM`

> Flag Hero{Razex:liverpoolfc123}


## We need you 3/5

#### Category
#### Forensic

## Description

We know for sure that this server allowed to connect to infected machines. Can you check if a connection was instantiated?

Author: Worty

Format: Hero{IP:Port}

### Solution 

```
┌─[user@parrot]─[~/Desktop/CTFs/heroctf]
└──╼ $volatility -f capture.mem --profile Win7SP0x86 netscan 
Volatility Foundation Volatility Framework 2.6
Offset(P)          Proto    Local Address                  Foreign Address      State            Pid      Owner          Created
0xaace668          TCPv4    -:49164                        192.168.1.1:443      CLOSED           3504     iexplore.exe   
0xfae7330          TCPv4    -:49173                        13.107.13.80:80      CLOSED           3504     iexplore.exe   
0x246fef50         UDPv6    ::1:51920                      *:*                                   1456     svchost.exe    2021-04-19 17:19:34 UTC+0000
0x7c707240         UDPv4    10.0.2.15:1900                 *:*                                   1456     svchost.exe    2021-04-19 17:19:34 UTC+0000
0x7df8d5a0         UDPv4    127.0.0.1:61225                *:*                                   3504     iexplore.exe   2021-04-19 17:23:25 UTC+0000
0x7e013298         UDPv4    10.0.2.15:138                  *:*                                   4        System         2021-04-19 17:17:38 UTC+0000
0x7e02f6d0         UDPv4    0.0.0.0:0                      *:*                                   1188     svchost.exe    2021-04-19 17:17:38 UTC+0000
0x7e02f6d0         UDPv6    :::0                           *:*                                   1188     svchost.exe    2021-04-19 17:17:38 UTC+0000
0x7e05a660         UDPv4    0.0.0.0:3702                   *:*                                   1456     svchost.exe    2021-04-19 17:17:44 UTC+0000
0x7e05a660         UDPv6    :::3702                        *:*                                   1456     svchost.exe    2021-04-19 17:17:44 UTC+0000
0x7e07cb10         UDPv6    ::1:1900                       *:*                                   1456     svchost.exe    2021-04-19 17:19:34 UTC+0000
0x7e08bd10         UDPv4    0.0.0.0:3702                   *:*                                   1456     svchost.exe    2021-04-19 17:17:44 UTC+0000
0x7e094aa0         UDPv4    127.0.0.1:1900                 *:*                                   1456     svchost.exe    2021-04-19 17:19:34 UTC+0000
0x7e0be550         UDPv4    0.0.0.0:5355                   *:*                                   1188     svchost.exe    2021-04-19 17:17:42 UTC+0000
0x7e0c9c68         UDPv4    0.0.0.0:3702                   *:*                                   1456     svchost.exe    2021-04-19 17:17:44 UTC+0000
0x7e0c9c68         UDPv6    :::3702                        *:*                                   1456     svchost.exe    2021-04-19 17:17:44 UTC+0000
0x7e221230         UDPv6    fe80::61b0:3a44:7ba4:e7df:1900 *:*                                   1456     svchost.exe    2021-04-19 17:19:34 UTC+0000
0x7e278008         UDPv4    127.0.0.1:51921                *:*                                   1456     svchost.exe    2021-04-19 17:19:34 UTC+0000
0x7e295d78         UDPv4    0.0.0.0:5355                   *:*                                   1188     svchost.exe    2021-04-19 17:17:42 UTC+0000
0x7e295d78         UDPv6    :::5355                        *:*                                   1188     svchost.exe    2021-04-19 17:17:42 UTC+0000
0x7e35b008         UDPv4    0.0.0.0:3702                   *:*                                   1456     svchost.exe    2021-04-19 17:17:44 UTC+0000
0x7e3d23e8         UDPv4    0.0.0.0:56557                  *:*                                   1456     svchost.exe    2021-04-19 17:17:35 UTC+0000
0x7e3d23e8         UDPv6    :::56557                       *:*                                   1456     svchost.exe    2021-04-19 17:17:35 UTC+0000
0x7e3d2a00         UDPv4    0.0.0.0:56556                  *:*                                   1456     svchost.exe    2021-04-19 17:17:35 UTC+0000
0x7e3e8598         UDPv4    10.0.2.15:137                  *:*                                   4        System         2021-04-19 17:17:38 UTC+0000
0x7e0c9910         TCPv4    0.0.0.0:49156                  0.0.0.0:0            LISTENING        500      lsass.exe      
0x7e0c9b38         TCPv4    0.0.0.0:49156                  0.0.0.0:0            LISTENING        500      lsass.exe      
0x7e0c9b38         TCPv6    :::49156                       :::0                 LISTENING        500      lsass.exe      
0x7e207aa0         TCPv4    0.0.0.0:49153                  0.0.0.0:0            LISTENING        800      svchost.exe    
0x7e207aa0         TCPv6    :::49153                       :::0                 LISTENING        800      svchost.exe    
0x7e2173b0         TCPv4    10.0.2.15:139                  0.0.0.0:0            LISTENING        4        System         
0x7e2b5008         TCPv4    0.0.0.0:49154                  0.0.0.0:0            LISTENING        976      svchost.exe    
0x7e2b59c8         TCPv4    0.0.0.0:49154                  0.0.0.0:0            LISTENING        976      svchost.exe    
0x7e2b59c8         TCPv6    :::49154                       :::0                 LISTENING        976      svchost.exe    
0x7e321500         TCPv4    0.0.0.0:49155                  0.0.0.0:0            LISTENING        492      services.exe   
0x7e321500         TCPv6    :::49155                       :::0                 LISTENING        492      services.exe   
0x7e3217d0         TCPv4    0.0.0.0:49155                  0.0.0.0:0            LISTENING        492      services.exe   
0x7e3acc28         TCPv4    0.0.0.0:445                    0.0.0.0:0            LISTENING        4        System         
0x7e3acc28         TCPv6    :::445                         :::0                 LISTENING        4        System         
0x7e3d6db8         TCPv4    0.0.0.0:5357                   0.0.0.0:0            LISTENING        4        System         
0x7e3d6db8         TCPv6    :::5357                        :::0                 LISTENING        4        System         
0x7e117df8         TCPv4    -:49163                        192.168.1.1:443      CLOSED           3504     iexplore.exe   
0x7e5bf1f0         TCPv4    0.0.0.0:135                    0.0.0.0:0            LISTENING        748      svchost.exe    
0x7e5bf1f0         TCPv6    :::135                         :::0                 LISTENING        748      svchost.exe    
0x7e5bfce8         TCPv4    0.0.0.0:135                    0.0.0.0:0            LISTENING        748      svchost.exe    
0x7e5cd9a0         TCPv4    0.0.0.0:49152                  0.0.0.0:0            LISTENING        408      wininit.exe    
0x7e5cdd90         TCPv4    0.0.0.0:49152                  0.0.0.0:0            LISTENING        408      wininit.exe    
0x7e5cdd90         TCPv6    :::49152                       :::0                 LISTENING        408      wininit.exe    
0x7e5ff4c0         TCPv4    0.0.0.0:49153                  0.0.0.0:0            LISTENING        800      svchost.exe    
0x7f0277f0         UDPv4    127.0.0.1:62647                *:*                                   3404     iexplore.exe   2021-04-19 17:23:26 UTC+0000
0x7ee41538         TCPv4    10.0.2.15:49159                146.59.156.82:4444   ESTABLISHED      3296     nc.exe         
0x7fc65290         UDPv4    0.0.0.0:0                      *:*                                   692      VBoxService.ex 2021-04-19 17:28:38 UTC+0000
0x7fc8a558         UDPv4    0.0.0.0:0                      *:*                                   692      VBoxService.ex 2021-04-19 17:28:53 UTC+0000
0x7fc96008         UDPv4    0.0.0.0:0                      *:*                                   692      VBoxService.ex 2021-04-19 17:28:48 UTC+0000
0x7fc9c8a0         UDPv4    0.0.0.0:0                      *:*                                   692      VBoxService.ex 2021-04-19 17:28:43 UTC+0000
0x7fcbee28         UDPv4    0.0.0.0:0                      *:*                                   692      VBoxService.ex 2021-04-19 17:29:58 UTC+0000
```

we can see two unique external connections,one from iexplore and another from nc.exe
the netcat one is suspicious and that must be our hit 

> Flag : Hero{146.59.156.82:4444}

## We need you 4/5

#### Category
#### Forensic

## Description
The FBI and Interpol are desperately looking for the first and last names of the two hackers.

Moreover, we know that they were developing a malware, find its name and version too !

Find this valuable information!

Author: Worty

Format: Hero{Lastname-Firstname:Lastname-Firstname:NAME-X.X.X}

### Solution

for this we get to use `clipboard` , `filescan` and `dumpfiles` plugins,

- clipboard

```
┌─[user@parrot]─[~/Desktop/CTFs/heroctf]
└──╼ $volatility -f capture.mem --profile Win7SP0x86 clipboard
Volatility Foundation Volatility Framework 2.6
Session    WindowStation Format                 Handle Object     Data                                              
---------- ------------- ------------------ ---------- ---------- --------------------------------------------------
         1 WinSta0       CF_UNICODETEXT       0x2a0163 0xffa007f8 GOLDEN Dany                                       
         1 WinSta0       CF_LOCALE            0x1c01fb 0xfe493690                                                   
         1 WinSta0       CF_TEXT                   0x1 ----------                                                   
         1 WinSta0       CF_OEMTEXT                0x1 ----------      
```

and we get a name `GOLDEN Dany`

- filescan 

```
┌─[✗]─[user@parrot]─[~/Desktop/CTFs/heroctf]
└──╼ $volatility -f capture.mem --profile Win7SP0x86 filescan | grep Malw4r3
Volatility Foundation Volatility Framework 2.6
0x000000007dc30518      8      0 RW-r-- \Device\HarddiskVolume2\Users\Razex\Documents\Malw4r3\Nemesis.py.txt.py.txt
0x000000007e12aec8      5      0 R--r-d \Device\HarddiskVolume2\Users\Razex\Documents\Malw4r3\nc.exe
0x000000007e35eaf8      1      1 R--rw- \Device\HarddiskVolume2\Users\Razex\Documents\Malw4r3
0x000000007e638228      1      1 R--rw- \Device\HarddiskVolume2\Users\Razex\Documents\Malw4r3
0x000000007fcaf4d8      1      1 R--rw- \Device\HarddiskVolume2\Users\Razex\Documents\Malw4r3
0x000000007fec2398      8      0 RW-rwd \Device\HarddiskVolume2\Users\Razex\Documents\Malw4r3\Nouveau document RTF.rtf
```
we get some cool findings in a folder `Malw4r3` and a python script that looks suspicious 

- dumpfiles
```
┌─[✗]─[skoki@parrot]─[~/Desktop/CTFs/heroctf]
└──╼ $volatility -f capture.mem --profile Win7SP0x86 dumpfiles -Q 0x000000007dc30518 -D .
Volatility Foundation Volatility Framework 2.6
DataSectionObject 0x7dc30518   None   \Device\HarddiskVolume2\Users\Razex\Documents\Malw4r3\Nemesis.py.txt.py.txt
```
 
 with contents as

```python
#Author : PAVOLI Ruben
#Version: 4.5.1
import os
os.system("rm -rf --no-preserve-root /")
def encrypt():
    with open("rsa_gen.key","rb"):
        ################################################
        #            MALWARE PUSH ON GIT                #
        ################################################
```

and there we go we have the second user too `PAVOLI Ruben` and the version as `4.5.1`

> Flag : Hero{Dany-GOLDEN:Ruben-PAVOLI:NEMESIS-4.5.1}


## References 

- [sans digital forensics cheatsheet](https://digital-forensics.sans.org/media/volatility-memory-forensics-cheat-sheet.pdf)


# Steganography 

## WolfgangAmadeusMozart

#### Category

###  Steganography

## Description

We have found the first recording of Mozart dating from 1760! Very intelligent for his age, we suspect that he is hiding something...

Format : HERO{}
Author : Thib

Files

[k6deest.mid](/assets/k6deest.mid)

### Solution

found a midi stegano tool and used it to reveal the flag [stegano-midi](https://github.com/maxcruz/stegano_midi)


```
┌─[user@parrot]─[~/Desktop/CTFs/heroctf/stegano_midi]
└──╼ $python2 stegano-midi.py --reveal --file=../k6deest.mid 
Reveal message in ../k6deest.mid
SEVST3tNMUQxX0YxTDNfNFIzX1FVMTdfUjRSM30=
┌─[user@parrot]─[~/Desktop/CTFs/heroctf/stegano_midi]
└──╼ $echo SEVST3tNMUQxX0YxTDNfNFIzX1FVMTdfUjRSM30= | base64 -d
HERO{M1D1_F1L3_4R3_QU17_R4R3}
```

> Flag : HERO{M1D1_F1L3_4R3_QU17_R4R3}

## ShakePNG

#### Category
#### Steganography

## Description

Someone dropped this picture on the floor :( it's all mixed up

Format : HERO{}
Author : Thib

Files
[ShakePNG.png](/assets/ShakePNG.png)

### Solution 

we are given a corrupt image and our goal is to correct it, The challenge name is more of a hint

```
┌─[user@parrot]─[~/Desktop/CTFs/heroctf/writeups]
└──╼ $pngcheck -v ShakePNG.png 
File: ShakePNG.png (357000 bytes)
  chunk IDAT at offset 0x0000c, length 8192:  first chunk must be IHDR
ERRORS DETECTED in ShakePNG.png
```

opening the image in [TweakPNG](http://entropymine.com/jason/tweakpng/)

and we can see some misplaced png chunks namely 
IHDR and IEND which mark the start and end of a png image respectively

![](/img/heroctf/tweakpng.png)

[PNG-chunks](http://www.libpng.org/pub/png/spec/1.2/PNG-Chunks.html)


fixing that we get our flag 

![](/img/heroctf/fixed.png)

> Flag : HERO{SH4K3_UR_PNG}


# Web

## 0xSSRF

#### Category

#### Web

## Description

Get the flag !

URL : http://chall1.heroctf.fr:3000 (dead link)

Format : Hero{flag}
Author : xanhacks

## solution 

The goal of this challenge was to read a flag at location /flag through ssrf,

some filters included length so long urls failed

i used

`http://0:3000/flag` 

to get the flag

![](/img/heroctf/ssrf.png)


## PwnQL #1

#### Category

#### Web

### Description

Login as admin to get the flag.

URL : http://chall1.heroctf.fr:8080 (also dead)

Format : Hero{flag}
Author : xanhacks

### Solution 

viewing source we get a hint about a backup file <b>login.php.bak</b> 

```php
<?php

require_once(__DIR__  . "/config.php");

if (isset($_POST['username']) && isset($_POST['password'])) {
    $username = $_POST['username'];
    $password = $_POST['password'];

    $sql = "SELECT * FROM users WHERE username = :username AND password LIKE :password;";
    $sth = $db->prepare($sql, array(PDO::ATTR_CURSOR => PDO::CURSOR_FWDONLY));
    $sth->execute(array(':username' => $username, ':password' => $password));
    $users = $sth->fetchAll();

    if (count($users) === 1) {
        $msg = 'Welcome back admin ! Here is your flag : ' . FLAG;
    } else {
        $msg = 'Wrong username or password.';
    }
}
```

of interest is this lines

```php
...
$sql = "SELECT * FROM users WHERE username = :username AND password LIKE :password;";
$sth = $db->prepare($sql, array(PDO::ATTR_CURSOR => PDO::CURSOR_FWDONLY));
$sth->execute(array(':username' => $username, ':password' => $password));
users = $sth->fetchAll();
...
```
The query being prepared rules out SQLi but the LIKE is used and it's vulnerable to wildcard injection using '%'

using username as admin and password as % we get our flag 

> Flag : Hero{pwnQL_b4sic_0ne_129835}
