---
title : Help Me,Memory Forensics Shakti 2021
tags : forensics steganography
---

Shakti took place over the weekend and i happened to miss out,
but i got a memory forensics challenge before it ended and i found it interesting 

## Challenge 

```
Help Me
400
re memory

Our department had taken up the responsibility of solving a mysterious case but unfortunately our system crashed. We could only recover this memory dump. Your job is get all the important files from the system and use the files to find out the secret informatiom.

Note : The flag consists of 3 parts.

Challenge Link - [Link](https://mega.nz/file/Qm5m0DJa#pr7uBldobIRECbBaRPPC6z6c40cMjeseHaOyuxplev8)

Challenge Author - v1Ru5 & bl4ck_Widw
```

- Tasks 

	- determine the profile 

	- find all the three parts of the flag

	- some reversing 


## Solution 


`volatility -f Challenge.vmem imageinfo` - determine the profile which we get as `Win7SP1x64`

- Basic recon 
list all the processes and check if any looks suspicious using module `pslist`


![](/img/shakti/pslist) 
 
nothing rings a bell 

...
proceeding the next step using `cmdscan` or `consoles` modules to check recent commands 

![](/img/shakti/cmdscan)

and we get a base64 encoded string

decoding that give us the first part of the flag 

```
┌─[skoki@parrot]─[~/Desktop/CTFs/shakti]
└──╼ $echo UGFydCAxlC0gc2hha3RpY3Rme0gwcDM= | base64 -d
Part 1�- shaktictf{H0p3
```
the second part of the flag was in files found using `filescan` module which reveals a 'Part II.png' file 
dumped by it's offset using `dumpfiles` module

![](/img/shakti/files.png)

...

we get an image file 

![](/img/shakti/flag2.png)

...
running `zsteg -` on it reveals the 2nd part of the flag as `_y0U_l1k3d_`


onto the last part of the flag, it was a deleted file, so we couldn't find it on most of the folders in the memdump,
our last option was to use the `mftparser` module 

`volatility -f Challenge.vmem --profile=Win7SP1x64 mftparser > mft_dump`

we get a hexdump of a L4STpy.zip 

```
0000000000: 50 4b 03 04 14 00 00 00 08 00 49 83 82 52 c5 07   PK........I..R..
0000000010: 91 03 96 01 00 00 8d 03 00 00 0b 00 00 00 4c 34   ..............L4
0000000020: 53 54 2e 70 79 2e 74 78 74 8d 53 5d 6b c2 30 14   ST.py.txt.S]k.0.
0000000030: 7d 5e c1 ff 70 0d 0c 1b 3a 3b 26 4e a1 98 87 31   }^..p...:;&N...1
0000000040: f6 03 7c f3 03 07 5d 8d 9a b5 26 25 49 41 1f f6   ..|...]...&%IA..
0000000050: df 77 d3 d8 aa c3 81 79 49 ee d7 b9 e7 5c 6e 0c   .w.....yI....\n.
0000000060: 1b 76 82 e0 c8 96 2b bc 16 fe ca f1 0a 3a c1 94   .v....+......:..
0000000070: 91 6a 37 18 7f 09 95 54 f3 c9 41 bf c5 04 83 6b   .j7....T..A....k
0000000080: be 81 23 37 a1 90 25 4d 30 0b f0 18 eb 0b dd 7b   ..#7..%M0......{
0000000090: a3 34 08 10 12 74 2a b7 1c c2 82 cb 3a b7 4d f6   .4...t*.....:.M.
00000000a0: 05 71 5a 96 5c ae c3 6c a7 43 a5 d7 2e 65 29 56   .qZ.\..l.C...e)V
00000000b0: b4 2f a2 21 a5 27 a8 52 0b 69 c3 5e 2f fe 56 42   ./.!.'.R.i.^/.VB
00000000c0: 86 c6 d2 88 fc 10 7a e2 f0 be e3 59 2e 3c 76 72   ......z....Y.<vr
00000000d0: b3 f7 ed d6 62 d3 fa 27 6c 7c 15 72 67 f1 1f b1   ....b..'l|.rg...
00000000e0: 97 48 b4 c4 dc e1 85 e1 49 27 b8 ab d8 6b 72 19   .H......I'...kr.
00000000f0: 9a db 4a cb 56 d3 82 36 72 ac 3e a2 6d f9 c1 3e   ..J.V..6r.>.m..>
0000000100: 19 a7 07 4e e9 a6 2a 2c 30 20 a4 71 dd d0 e8 aa   ...N..*,0..q....
0000000110: 50 64 4d e1 21 db a5 1a 0b 9c 0f 5b 77 82 4b e1   PdM.!......[w.K.
0000000120: 2e 16 0b 23 ab 3d d7 22 0b 29 fd 2b c0 f7 8b 58   ...#.=.".).+...X
0000000130: 2b c0 55 a0 76 4a 2f c6 c4 8b 33 54 85 7a b5 07   +.U.vJ/...3T.z..
0000000140: 82 1b 48 10 31 70 50 67 2c 88 c0 f4 47 af 14 1e   ..H.1pPg,...G...
0000000150: 61 30 42 03 9f 97 a5 a7 a9 c2 1d ac 3e cf ac fc   a0B.........>...
0000000160: 54 9b 9e a8 69 c6 70 f2 95 0d c9 87 b4 5c 43 6d   T...i.p......\Cm
0000000170: 24 00 f5 f2 e4 ac 59 1d 3f f3 19 0e dc f9 fd b6   $.....Y.?.......
0000000180: e5 0e 13 f5 4d 19 cb db e5 f0 21 32 57 2a 8e a1   ....M.....!2W*..
0000000190: 50 2a 37 50 88 9c c3 51 55 1a 36 45 ba 05 61 20   P*7P...QU.6E..a.
00000001a0: 53 fb b2 e0 96 77 bb a4 e1 e5 fe c9 cc 19 41 ad   S....w........A.
00000001b0: eb 1a 0d bb 43 ba 4d 85 4c 9e 91 58 1d f9 05 50   ....C.M.L..X...P
00000001c0: 4b 01 02 1f 00 14 00 00 00 08 00 49 83 82 52 c5   K..........I..R.
00000001d0: 07 91 03 96 01 00 00 8d 03 00 00 0b 00 24 00 00   .............$..
00000001e0: 00 00 00 00 00 20 00 00 00 00 00 00 00 4c 34 53   .............L4S
00000001f0: 54 2e 70 79 2e 74 78 74 0a 00 20 00 00 00 00 00   T.py.txt........
0000000200: 01 00 18 00 da a8 f1 ce ae 27 d7 01 35 ee 46 c8   .........'..5.F.
0000000210: ae 27 d7 01 35 ee 46 c8 ae 27 d7 01 50 4b 05 06   .'..5.F..'..PK..
0000000220: 00 00 00 00 01 00 01 00 5d 00 00 00 bf 01 00 00   ........].......
0000000230: 00 00                                             ..
```
save that in a txt file,then 

```cat file.txt | xxd -r > L4STPY.zip```

`7z x L4STPY.zip`

we get a python code that we need to reverse to get the last part of the Flag 


```python
s=4

y=[]

Z=[]

k=[]

Q="uh27bio:uY<xrA."

def yes(inp):

    st=[]

    for i in range (len(inp)):

        st.append(chr(ord(inp[i])-i+4))

    print(''.join(st)+"}")

def Checkin(inp):

    for i in range(len(inp)):

        if(len(inp)<=7):

            Z.append(chr(ord(inp[i])-1+i))

        else:

            Z.append(chr(ord(inp[i])+4))
    return(''.join(Z))

def tryin(text,s):
 
    result = ""
 
    for i in range(len(text)):     	char = text[i]

        if(char.isnumeric()):

            result+=(chr(ord(char)-1))

        elif(char.isupper()):
 
            result += chr((ord(char) + s-65) % 26 + 65)
 
        else:
 
            result+=(chr(ord(char)^1))

    return result 

X=input("Enter input:  ")

k=Checkin(tryin(X,s))

print(k)

if(Q==k):

    print("Yoo.. looks like your flag is complete!!")

    yes(X)


else:

    print("try again:/ ")
```
reversing the python code and running it spits the last part of the flag as `ch4lL3ng3!}`

> Flag:shaktictf{H0p3_y0U_l1k3d_ch4lL3ng3!}

PS: i am told it was worth 400 points and was among the least solved XD

## further reading 

- [Art of Memory Forensics](https://www.amazon.com/Art-Memory-Forensics-Detecting-Malware/dp/1118825098)

- [dfir.traing,volatility cheatsheet](https://www.dfir.training/memory-cheats/320-memory-forensics-cheat-sheet-v1-2/file)
