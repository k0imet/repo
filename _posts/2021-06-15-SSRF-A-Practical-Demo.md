---
title : SSRF,A Practical Demo
tags : ssrf
---

# Whoami 

```
┌─[koimet@parrot]─[~]
└──╼ $whoami
koimet

- captain fr334aks

```

# SSRF 

## What is SSRF

- a web security vulnerability that allows an attacker to induce the server-side
application to make HTTP requests to an arbitrary domain of the attacker's 
choosing. 
	~portswigger

- a Web security that lets attackers send requests from the server to other 
resources, both internal and external, and receive responses. 
	~acunetix


## Why SSRF

- Despite being old in the field it's still a less known vulnerability

- impactful to businesses when not careful enough

- if you look closely it's almost everywhere 

- despite not being listed in owasp top 10 list in 2017 it's a probable candidate in coming years


## Basics 

- Basic understanding of web and how it works,

- some protocols and schemas used in web technologies resources

- basic understanding of php (for this lab)


...

- Assuming you have found a vulnerable instance,what's next ??

## What to try 

- Accessing local files (file://)

- Access local ips 
	
	- Local Ip Bypass

	- DNS Spoofing  

	- DNS Rebinding 

- Try to make an internal asset discovery and internal port scan owasp

- Access Private content (Filtered by IP or only accesible locally like /admin path)

## How to test for SSRFs

- The best way to discover SSRF vulnerabilities is a manual code review to see if all URL inputs are being validated. However, when source code 
is not available and when a full code review is not possible, efforts should be focused on testing the features that are most prone to SSRF.

- SSRFs occur when a server requires external resources. For example, sometimes a web application would need to create a thumbnail from a URL
of an image, or create a screenshot of a video from another site (like youtube.com). If a server doesn’t restrict access to internal
resources, SSRF vulnerabilities occur.

- The following page on public.example.com allows users to upload a profile photo from the Internet 

	```https://public.example.com/upload_profile_from_url.php?url=www.google.com/cute_pugs.jpeg```


- In order to fetch cute_pugs.jpeg from google.com, the web application would have to visit and retrieve contents from google.com. If the
 server doesn’t make a distinction between internal and external resources, an attacker could just as easily request

	```https://public.example.com/upload_profile_from_url.php?url=localhost/secret_password_file.txt```

And make the webserver display the file that contains the password to the webserver.

Features that are often vulnerable to SSRF include webhooks, file upload via URL, document and image processors, link expansion, and 
proxy services (these features all require visiting and fetching external resources). However, it is worth testing any endpoint that processes
a user-provided URL.



## How do I prevent SSRF vulnerability?

-It is recommended to restrict using network calls if it’s not required, since it
can lead to sensitive information exposure as well — or at least limit the
ability to perform network calls to the internal network or critical resources.


.... 

We should try out some labs 

Courtesy of CyberRanges 
