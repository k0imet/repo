---
title : Hackpack 2020 Sound CTF Writeup
tags : sstv
---

![](/img/hackpack/logo.png)
This challenge was among the most cheeky challenges of all
running in the misc category

Took me several days to finally join the pieces together as we are given a Wave media file by name Scottie.wav
…….
listening to it gave beep sounds at intervals like a transmission
which led to me deciphering the transmission as Slow Scan tv (SSTV)

Now i had to decode that and two tools came to my mind
….
Qsstv for linux and rx sstv for Windows
…….

and here is the solution using Qsstv
install it via

```bash
sudo apt update && apt install qsstv
```

opening the wave audio file gave as the flag
and there we got our flag
![](/img/hackpack/flag.jpg)

> flag{th1s_1s_sl0w_me3ss@ge}

that gave us 100 points

the mode of transmission however was not Scottie as the name but actually robot36
