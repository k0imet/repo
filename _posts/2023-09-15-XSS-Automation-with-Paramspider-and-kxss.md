---
layout: article
title : XSS Automation with Paramspider and kxss
tags : xss web
---

# Introduction 

- In this post we will cover the basics of XSS automation using Paramspider and  kxss applicable in pentesting and Bug Bounties


## Prerequisites

- Paramspider

- kxss 



## What is XSS 

XSS (Cross-Site Scripting) is a type of security vulnerability that can be found in web 
applications. It allows an attacker to inject malicious code, such as scripts, into a web page viewed by other users. This can lead to the theft of sensitive information, such as login credentials or personal data, and can also be used to hijack user sessions or redirect users to malicious websites. The severity of an XSS vulnerability can range from minor annoyance to significant risk to the security of a web application and its users.

## What are parameters 

"....URL parameters or query strings are the part of a URL that typically comes after a question mark (?) and are used to pass data along with the URL. They can be active parameters that modify page content or passive parameters that are mostly used for tracking and do not change the content" [url parameters](https://ahrefs.com/blog/url-parameters/#:~:text=URL%20parameters%20or%20query%20strings,do%20not%20change%20the%20content.)


## What is Paramspider

Paramspider is a tool by [devanshbatham](https://github.com/devanshbatham) that allows one to fetch URLs related to any domain or a list of domains from Wayback achives. It also filters out "boring" URLs, allowing you to focus on the ones that matter the most. [About](https://github.com/devanshbatham/ParamSpider)

### installation 

```bash 
▶ git clone https://github.com/devanshbatham/paramspider
▶ cd paramspider
▶ pip install .
```

### usage 

```bash 
paramspider -d example.com
```
![image](https://github.com/k0imet/k0imet.github.io/assets/60982828/fefa7df4-fe02-4f34-a2cc-4a4f14a5d479)


## What is kxss

[Emoe's kxss](https://github.com/Emoe/kxss) is a tool adapted from [Tomnomnom's kxss hacks script](https://github.com/tomnomnom/hacks/tree/master/kxss) the general idea behind the tools logic is that : 

- Take URLs with params on stdin. These might have come from waybackurls or maybe a Burp session
- Request the URLs, check the response body for any reflected parameters. There will be many false positives here.
- For any reflected parameters, re-request with some random alphanumeric value appended to the param, Only one param is appended to at a time. This is to avoid breaking the request when a different param is required


### Installation 

```bash 

▶ go install https://github.com/Emoe/kxss@latest
```

### usage

- In our case we will use the urls fethed from Parampider into kxss

```bash 

▶ cat output/example.com.txt | kxss
```

![image](https://github.com/k0imet/k0imet.github.io/assets/60982828/77998027-b9f9-4eaf-a9ad-ebb66ccf9d10)



Cleaning up the results

```python 

input_filename = "unfiltered.txt"
output_filename = "filtered_output.txt"

with open(input_filename, 'r') as infile, open(output_filename, 'w') as outfile:
    for line in infile:
        if "Unfiltered: []" not in line:
            outfile.write(line)

print(f"Filtered results have been saved to {output_filename}")

## simple python script to remove empty Unfiltered [] results from kxss output
```

![image](https://github.com/k0imet/k0imet.github.io/assets/60982828/b51746c9-86e8-476f-abf1-fb5712a62778)



- Having a smaller list of potentially vulnerable parameters with unfiltered characters we can proceed to craft xss payloads that may trigger, 
otherwise one can use other xss automation tools like Xsstrike, Nuclei, Dalfox etc. 


## POC 

![image](https://github.com/k0imet/k0imet.github.io/assets/60982828/158ac929-c2c1-4b46-859f-a0c6006e4e9e)

