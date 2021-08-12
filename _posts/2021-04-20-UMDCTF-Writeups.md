---
title : UMDCTF Writeups
tags : steganography wireshark aircrack-ng
---

![](/img/umdctf/scoreboard.png)

we settled for #43 from 484 teams


# Misc


more writeups from Mystik0ri0n [here](https://github.com/mystickev/CTFwriteups)

## John's Return 

### Description 

![](/img/umdctf/john.png)

you can get the challenge file here 

[received.pcapng](https://github.com/sajjadium/ctf-archives/blob/master/UMDCTF/2021/misc/John_Return/received.pcapng)

### Solution 

#### summary : 
- WPA traffic decrypting 

Here we have a pcapng file wirh 802.11 radiotap (wireless) traffic dump,seems it's encrypted 

![](/img/umdctf/wireshark1.png)

....
but wait it's a .pcapng file which aircrack-ng is unable to read it so we are forced to convert to a pcap 
i used this site [pcapng.com](https://pcapng.com) for that 
then we can use aircrack-ng with rockyou.txt as our wordlist, 
seconds later it spits out the password 

```
aircrack-ng received.s0i0.pcap -w /usr/share/wordlists/rockyou.txt 

      [00:00:00] 48/7120712 keys tested (2246.56 k/s) 

      Time left: 52 minutes, 50 seconds                          0.00%

                           KEY FOUND! [ chocolate ]


      Master Key     : F8 FA E8 1B 93 20 46 CD F5 7C 0B 7C D0 70 0C 11 
                       F5 70 AD 93 5B 9C 91 97 8D 44 18 70 76 56 5E B6 

      Transient Key  : C7 DE 67 E5 E6 B2 C9 77 99 97 14 1C D2 88 60 60 
                       10 05 FF CC 55 7A BF 51 63 CF 11 FilippoG98FilippoG98FilippoG9897 38 86 32 FF 
                       AB 93 AA 76 0A E9 6B 85 72 70 26 ED 65 48 8C 44 
                       22 A1 AC FA 0F 0B 6E 3D 1F 96 7B F7 C7 30 E5 54 

      EAPOL HMAC     : B3 22 03 F5 85 25 3E 03 EE 64 24 6C C2 CB 77 76 
```
using the password as `chocolate` and the SSID as `linksys` we can decrypt the traffic 

open wireshark > edit > preferences > protocols > IEEE 802.11 > Enable Decryption > Key type : wpa-pwd > key 
password:SSID final as wpa-pwd chocolate:linksys

![](/img/umdctf/key1.png)

![](/img/umdctf/key0.png)

looking back at the decrpted traffic we get our flag in the decrypted CCMP data 

![](/img/umdctf/flag1.png)

> Flag : UMDCTF-{wh3r3_j0hn}


# Steganography 


## Pickle Rick

### Challenge 

![](/img/umdctf/pickle.png)


[Download Here](https://drive.google.com/drive/folders/135epDZ18MdIycbBt_Fekbi8hdF-83v6Y)

### Solution 

We are given two rick rolls :joy: but both contain a hidden message, the first had the steg password for the second

```
┌─[@parrot]─[~/Desktop/CTFs/umdctf]
└──╼ $steghide extract -sf together-forever-encoded.wav 
Enter passphrase: 
wrote extracted data to "steganopayload318205.txt".
┌─[@parrot]─[~/Desktop/CTFs/umdctf]
└──╼ $cat steganopayload318205.txt 
The password is "big_chungus"!
┌─[@parrot]─[~/Desktop/CTFs/umdctf]
└──╼ $steghide extract -sf rickroll.wav 
Enter passphrase: 
wrote extracted data to "steganopayload318287.txt".
┌─[@parrot]─[~/Desktop/CTFs/umdctf]
└──╼ $cat steganopayload318287.txt 
UMDCTF-{n3v3r_g0nna_l3t_y0u_d0wn}

```

> Flag : UMDCTF-{n3v3r_g0nna_l3t_y0u_d0wn}



## Hidden Card 


### Challenge 

![](/img/umdctf/hidden.png)


### Solution 

having tried majority of Steganographic techniques and failed,decided to try one last tool 

[diit](http://diit.sourceforge.net/)

opening the image in it and decoding using BattleSteg algorithm we get our Flag 

![](/img/umdctf/diit.png)


![](/img/umdctf/success.png)


```
┌─[@parrot]─[~/Desktop/CTFs/umdctf]
└──╼ $cat battle.txt 
UMDCTF-{c4rd_1n_a_c4rd$t4ck}
```


> Flag : UMDCTF-{c4rd_1n_a_c4rd$t4ck}
