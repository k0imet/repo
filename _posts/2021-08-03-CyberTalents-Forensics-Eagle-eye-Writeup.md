---
layout: article
title: CyberTalents Forensics Eagle Eye Writeup
tags: forensics memory-forensics volatility
---


# Eagle Eye

![](/img/CyberTalents/eagle.png)

- Eagle eye is a memory forensics challenge rated between hard and insane,
as of writing this writeup the challenge has 15 solves in 4 months


## TL:DR 

- Windows memory forensics using volatility2

- using mftdump vol2 plugin

### Challenge Description 

When you deal with an attacker, don’t always trust what you see.


### Solution 

- Inorder to solve this challenge successfully we are to first determine a suitable profile to use.

using the syntax 

```bash
volatility -f chall.raw imageinfo
```

running the command above we get a list of viable profiles,
for this writeup i will use the first one *Win7SP1x86_23418* 


![profile.png](/img/CyberTalents/profile.png)


my initial analysis included checking for low hanging fruits, this included checking for rogue processes, files on the machine,browser histories,recent commands on the machine to no success.

but going back to the challenge description there's a hint `...don't always trust what you see`

for this i thought of MTFdump plugin for further analysis.

syntax for the command 

```bash
volatility -f chall.raw --profile Win7SP1x86_23418 mftdump > mftdump

```

after running for a while we get our mftdump data 


which we start grepping and searching for strings that can and probably will match our flag format in this case `flag{.*}`  since manual analysis would have been impossible as there were around 38054 lines of data to look at 

searching for the string flag we get a hit 
```bash 
$FILE_NAME
Creation                       Modified                       MFT Altered                    Access Date                    Name/Path
------------------------------ ------------------------------ ------------------------------ ------------------------------ ---------
2021-02-15 14:09:51 UTC+0000 2021-02-15 14:19:44 UTC+0000   2021-02-15 14:19:44 UTC+0000   2021-02-15 14:09:51 UTC+0000   $Recycle.Bin\S-1-5-21-4163927476-1738762144-3755410103-1000\$R1ZVDL9.cpp

$OBJECT_ID
Object ID: f0aa160a-956f-eb11-be46-0800273856bc
Birth Volume ID: 80000000-1802-0000-0000-180000000600
Birth Object ID: fb010000-1800-0000-2369-6e636c756465
Birth Domain ID: 203c696f-7374-7265-616d-3e0d0a766f69

$DATA
0000000000: 23 69 6e 63 6c 75 64 65 20 3c 69 6f 73 74 72 65   #include.<iostre
0000000010: 61 6d 3e 0d 0a 76 6f 69 64 20 66 6c 61 67 28 29   am>..void.flag()
0000000020: 3b 0d 0a 75 73 69 6e 67 20 6e 61 6d 65 73 70 61   ;..using.namespa
0000000030: 63 65 20 73 74 64 3b 0d 0a 69 6e 74 20 6d 61 69   ce.std;..int.mai
0000000040: 6e 28 29 7b 0d 0a 0d 0a 66 6c 61 67 28 29 3b 0d   n(){....flag();.
0000000050: 0a 77 68 69 6c 65 28 74 72 75 65 29 7b 0d 0a 7d   .while(true){..}
0000000060: 0d 0a 72 65 74 75 72 6e 20 30 3b 0d 0a 7d 0d 0a   ..return.0;..}..
0000000070: 0d 0a 76 6f 69 64 20 66 6c 61 67 28 29 7b 0d 0a   ..void.flag(){..
0000000080: 63 68 61 72 20 78 5b 32 36 5d 3b 0d 0a 69 6e 74   char.x[26];..int
0000000090: 20 6e 3d 30 3b 0d 0a 78 5b 6e 2b 2b 5d 3d 27 66   .n=0;..x[n++]='f
00000000a0: 27 3b 0d 0a 78 5b 6e 2b 2b 5d 3d 27 6c 27 3b 0d   ';..x[n++]='l';.
00000000b0: 0a 78 5b 6e 2b 2b 5d 3d 27 61 27 3b 0d 0a 78 5b   .x[n++]='a';..x[
00000000c0: 6e 2b 2b 5d 3d 27 67 27 3b 0d 0a 78 5b 6e 2b 2b   n++]='g';..x[n++
00000000d0: 5d 3d 27 32 27 3b 0d 0a 78 5b 6e 2b 2b 5d 3d 27   ]='2';..x[n++]='
00000000e0: 3a 27 3b 0d 0a 78 5b 6e 2b 2b 5d 3d 27 5f 27 3b   :';..x[n++]='_';
00000000f0: 0d 0a 78 5b 6e 2b 2b 5d 3d 27 53 27 3b 0d 0a 78   ..x[n++]='S';..x
0000000100: 5b 6e 2b 2b 5d 3d 27 59 27 3b 0d 0a 78 5b 6e 2b   [n++]='Y';..x[n+
0000000110: 2b 5d 3d 27 53 27 3b 0d 0a 78 5b 6e 2b 2b 5d 3d   +]='S';..x[n++]=
0000000120: 27 5f 27 3b 0d 0a 78 5b 6e 2b 2b 5d 3d 27 50 27   '_';..x[n++]='P'
0000000130: 3b 0d 0a 78 5b 6e 2b 2b 5d 3d 27 72 27 3b 0d 0a   ;..x[n++]='r';..
0000000140: 78 5b 6e 2b 2b 5d 3d 27 63 27 3b 0d 0a 78 5b 6e   x[n++]='c';..x[n
0000000150: 2b 2b 5d 3d 27 65 27 3b 0d 0a 78 5b 6e 2b 2b 5d   ++]='e';..x[n++]
0000000160: 3d 27 33 27 3b 0d 0a 78 5b 6e 2b 2b 5d 3d 27 33   ='3';..x[n++]='3
0000000170: 27 3b 0d 0a 78 5b 6e 2b 2b 5d 3d 27 5f 27 3b 0d   ';..x[n++]='_';.
0000000180: 0a 78 5b 6e 2b 2b 5d 3d 27 34 27 3b 0d 0a 78 5b   .x[n++]='4';..x[
0000000190: 6e 2b 2b 5d 3d 27 74 27 3b 0d 0a 78 5b 6e 2b 2b   n++]='t';..x[n++
00000001a0: 5d 3d 27 34 27 3b 0d 0a 78 5b 6e 2b 2b 5d 3d 27   ]='4';..x[n++]='
00000001b0: 4c 27 3b 0d 0a 78 5b 6e 2b 2b 5d 3d 27 4c 27 3b   L';..x[n++]='L';
00000001c0: 0d 0a 78 5b 6e 2b 2b 5d 3d 27 7d 27 3b 0d 0a 66   ..x[n++]='}';..f
00000001d0: 6f 72 20 28 69 6e 74 20 69 20 3d 30 3b 20 69 3c   or.(int.i.=0;.i<
00000001e0: 6e 3b 69 2b 2b 29 7b 0d 0a 09 28 78 5b 6e 5d 29   n;i++){...(x[n])
00000001f0: 3b 0d 0a 7d 0d 0a 7d 0d 0a 0d 0a                  ;..}..}....

***************************************************************************
***************************************************************************
MFT entry found at offset 0x8051c00
Attribute: File
Record Number: 47867
Link count: 2
```

which looks like a hexdump of a cpp code ... 

i simply copied it and converted the data back to a readable code 

```bash 
0000000000: 23 69 6e 63 6c 75 64 65 20 3c 69 6f 73 74 72 65   #include.<iostre
0000000010: 61 6d 3e 0d 0a 76 6f 69 64 20 66 6c 61 67 28 29   am>..void.flag()
0000000020: 3b 0d 0a 75 73 69 6e 67 20 6e 61 6d 65 73 70 61   ;..using.namespa
0000000030: 63 65 20 73 74 64 3b 0d 0a 69 6e 74 20 6d 61 69   ce.std;..int.mai
0000000040: 6e 28 29 7b 0d 0a 0d 0a 66 6c 61 67 28 29 3b 0d   n(){....flag();.
0000000050: 0a 77 68 69 6c 65 28 74 72 75 65 29 7b 0d 0a 7d   .while(true){..}
0000000060: 0d 0a 72 65 74 75 72 6e 20 30 3b 0d 0a 7d 0d 0a   ..return.0;..}..
0000000070: 0d 0a 76 6f 69 64 20 66 6c 61 67 28 29 7b 0d 0a   ..void.flag(){..
0000000080: 63 68 61 72 20 78 5b 32 36 5d 3b 0d 0a 69 6e 74   char.x[26];..int
0000000090: 20 6e 3d 30 3b 0d 0a 78 5b 6e 2b 2b 5d 3d 27 66   .n=0;..x[n++]='f
00000000a0: 27 3b 0d 0a 78 5b 6e 2b 2b 5d 3d 27 6c 27 3b 0d   ';..x[n++]='l';.
00000000b0: 0a 78 5b 6e 2b 2b 5d 3d 27 61 27 3b 0d 0a 78 5b   .x[n++]='a';..x[
00000000c0: 6e 2b 2b 5d 3d 27 67 27 3b 0d 0a 78 5b 6e 2b 2b   n++]='g';..x[n++
00000000d0: 5d 3d 27 32 27 3b 0d 0a 78 5b 6e 2b 2b 5d 3d 27   ]='2';..x[n++]='
00000000e0: 3a 27 3b 0d 0a 78 5b 6e 2b 2b 5d 3d 27 5f 27 3b   :';..x[n++]='_';
00000000f0: 0d 0a 78 5b 6e 2b 2b 5d 3d 27 53 27 3b 0d 0a 78   ..x[n++]='S';..x
0000000100: 5b 6e 2b 2b 5d 3d 27 59 27 3b 0d 0a 78 5b 6e 2b   [n++]='Y';..x[n+
0000000110: 2b 5d 3d 27 53 27 3b 0d 0a 78 5b 6e 2b 2b 5d 3d   +]='S';..x[n++]=
0000000120: 27 5f 27 3b 0d 0a 78 5b 6e 2b 2b 5d 3d 27 50 27   '_';..x[n++]='P'
0000000130: 3b 0d 0a 78 5b 6e 2b 2b 5d 3d 27 72 27 3b 0d 0a   ;..x[n++]='r';..
0000000140: 78 5b 6e 2b 2b 5d 3d 27 63 27 3b 0d 0a 78 5b 6e   x[n++]='c';..x[n
0000000150: 2b 2b 5d 3d 27 65 27 3b 0d 0a 78 5b 6e 2b 2b 5d   ++]='e';..x[n++]
0000000160: 3d 27 33 27 3b 0d 0a 78 5b 6e 2b 2b 5d 3d 27 33   ='X;..x[n++]='3
0000000170: 27 3b 0d 0a 78 5b 6e 2b 2b 5d 3d 27 5f 27 3b 0d   ';..x[n++]='_';.
0000000180: 0a 78 5b 6e 2b 2b 5d 3d 27 34 27 3b 0d 0a 78 5b   .x[n++]='X;..x[
0000000190: 6e 2b 2b 5d 3d 27 74 27 3b 0d 0a 78 5b 6e 2b 2b   n++]='X;..x[n++
00000001a0: 5d 3d 27 34 27 3b 0d 0a 78 5b 6e 2b 2b 5d 3d 27   ]='X;..x[n++]='
00000001b0: 4c 27 3b 0d 0a 78 5b 6e 2b 2b 5d 3d 27 4c 27 3b   L';..x[n++]='L';
00000001c0: 0d 0a 78 5b 6e 2b 2b 5d 3d 27 7d 27 3b 0d 0a 66   ..x[n++]='}';..f
00000001d0: 6f 72 20 28 69 6e 74 20 69 20 3d 30 3b 20 69 3c   or.(int.i.=0;.i<
00000001e0: 6e 3b 69 2b 2b 29 7b 0d 0a 09 28 78 5b 6e 5d 29   n;i++){...(x[n])
00000001f0: 3b 0d 0a 7d 0d 0a 7d 0d 0a 0d 0a                  ;..}..}....

```
then 

*cat hexdump | xxd -r > code.cpp*

after beautifying the code we get the second part of the Flag 

```cpp
#include <iostream>

void flag();
using namespace std;
int main() {
  flag();
  while (true) {

  }
  return 0;
}

void flag() {
  char x[26];
  int n = 0;
  x[n++] = 'f';
  x[n++] = 'l';
  x[n++] = 'a';
  x[n++] = 'g';
  x[n++] = '2';
  x[n++] = ':';
  x[n++] = '_';
  x[n++] = 'S';
  x[n++] = 'Y';
  x[n++] = 'S';
  x[n++] = '_';
  x[n++] = 'P';
  x[n++] = 'r';
  x[n++] = 'c';
  x[n++] = 'e';
  x[n++] = '3';
  x[n++] = '3';
  x[n++] = '_';
  x[n++] = '4';
  x[n++] = 't';
  x[n++] = 'X;
  x[n++] = 'X;
  x[n++] = 'X;
  x[n++] = '}';
  for (int i = 0; i < n; i++) {
    cout << x << "\n";
  }
}


```
_redacted some chars off the flag to curb laziness XD_

it was just a basic C++ code that loops through the Flag characters 

compiling it and running it gives us 

```bash
$./code 
flag2:_SYS_Prce33_4txx}
flag2:_SYS_Prce33_4t4xx}
flag2:_SYS_Prce33_4t4xx}
flag2:_SYS_Prce33_4t4xx}
flag2:_SYS_Prce33_4t4xx}
flag2:_SYS_Prce33_4t4xx}
flag2:_SYS_Prce33_4t4xx}
flag2:_SYS_Prce33_4t4xx}
flag2:_SYS_Prce33_4t4xx}
flag2:_SYS_Prce33_4t4xx}
flag2:_SYS_Prce33_4t4xx}
flag2:_SYS_Prce33_4t4xx}
flag2:_SYS_Prce33_4t4xx}
flag2:_SYS_Prce33_4t4xx}
flag2:_SYS_Prce33_4t4xx}
flag2:_SYS_Prce33_4t4xx}
flag2:_SYS_Prce33_4t4xx}
flag2:_SYS_Prce33_4t4xx}
flag2:_SYS_Prce33_4t4xx}
flag2:_SYS_Prce33_4t4xx}
flag2:_SYS_Prce33_4t4xx}
flag2:_SYS_Prce33_4t4xx}
flag2:_SYS_Prce33_4t4xx}
flag2:_SYS_Prce33_4t4xx}

```

Armed with the second part of the flag, we go back hunting for the first part inorder to get the full flag 

searching for string `part` in the mftdump we are gifted with the first part 

```bash
$FILE_NAME
Creation                       Modified                       MFT Altered                    Access Date                    Name/Path
------------------------------ ------------------------------ ------------------------------ ------------------------------ ---------
2021-02-15 13:31:58 UTC+0000 2021-02-15 14:35:19 UTC+0000   2021-02-15 14:35:19 UTC+0000   2021-02-15 13:31:58 UTC+0000   Users\MM0X\Desktop\1st.txt

$OBJECT_ID
Object ID: 60948d0e-926f-eb11-8c93-0800273856bc
Birth Volume ID: 80000000-8000-0000-0000-180000000100
Birth Object ID: 61000000-1800-0000-596f-7520476f7420
Birth Domain ID: 4d652062-7574-2074-6861-742773206f6e

$DATA
0000000000: 59 6f 75 20 47 6f 74 20 4d 65 20 62 75 74 20 74   You.Got.Me.but.t
0000000010: 68 61 74 27 73 20 6f 6e 6c 79 20 74 68 65 20 66   hat's.only.the.f
0000000020: 69 72 73 74 20 48 61 6c 66 20 66 69 6e 64 20 74   irst.Half.find.t
0000000030: 68 65 20 54 68 65 20 72 65 73 74 20 4f 46 20 69   he.The.rest.OF.i
0000000040: 74 20 0d 0a 0d 0a 31 73 74 70 61 72 74 3a 7b 44   t.....1stpart:{D
0000000050: 6f 6e 30 74 5f 61 6c 77 34 79 73 5f 54 52 75 73   on0t_xxxxxx_Txxx
0000000060: 74                                                x

***************************************************************************
***************************************************************************
MFT entry found at offset 0x28eb000
Attribute: In Use & File
Record Number: 33636
Link count: 1

```
converting the hexdump back to readable format we can read off the First part of the flag as

```bash  
cat 1st.txt 
You Got Me but that's only the first Half find the The rest OF it 

1stpart:{Don0t_xxxxxx_TRxxx

```


combining both parts we get the flag as 

> Flag : flag{Don0t_xxxxxx_TRxxx_SYS_Prce33_4t4xx}

hoping that was the intended solution :) 

for any clarification or suggestions including more writeups kindly reach me on [twitter](https://twitter.com/k0imet_)
