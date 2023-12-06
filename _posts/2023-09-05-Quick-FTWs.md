---
layout: article
title: Quick FTWs
tags: python2 wget
---

## For the Wins (FTW)

- Below will be a compilation of one-liners and other tricks to solve common issues that i come across



#### Installing Python2.7 on Kali Linux 

```bash 
sudo apt install python2.7 python2.7-dev -y && curl -O https://bootstrap.pypa.io/pip/2.7/get-pip.py && sudo python2.7 get-pip.py
```

#### Fixing the invalid "egg_info" command 

```bash 
'"'"'/tmp/pip-req-build-aSU9AP/setup.py'"'"'; __file__='"'"'/tmp/pip-req-build-aSU9AP/setup.py'"'"';f=getattr(tokenize, '"'"'open'"'"', open)(__file__);code=f.read().replace('"'"'\r\n'"'"', '"'"'\n'"'"');f.close();exec(compile(code, __file__, '"'"'exec'"'"'))' egg_info --egg-base /tmp/pip-pip-egg-info-GmrXHL
         cwd: /tmp/pip-req-build-aSU9AP/
    Complete output (10 lines):
    /usr/lib/python2.7/distutils/dist.py:267: UserWarning: Unknown distribution option: 'extras_require'
      warnings.warn(msg)
    /usr/lib/python2.7/distutils/dist.py:267: UserWarning: Unknown distribution option: 'install_requires'
      warnings.warn(msg)
    usage: setup.py [global_opts] cmd1 [cmd1_opts] [cmd2 [cmd2_opts] ...]
       or: setup.py --help [cmd1 cmd2 ...]
       or: setup.py --help-commands
       or: setup.py cmd --help
    
    error: invalid command 'egg_info'

```

Solution

```bash 
pip2 install --upgrade pip setuptools
```

or 

```bash 
pip2 install . --no-use-pep517
```

#### WGET Recursive Download

-
```bash
wget -r -np -nH --cut-dirs=1 -A . http://sites.com/dir/
```


#### Medium Paywall bypass 

```http://webcache.googleusercontent.com/search?q=cache:mediumlinkehere```

#### Fixing Elrepo mirror issues 

# how it starts 

```bash
sudo yum install elrepo-release epel-release
```
But from there the repo becomes unusable 

to fix that 

```sudo dnf clean all```
```dnf makecache --refresh```


To fix the mirror issue edit /etc/yum.repos.d/elrepo/elrepo.repo, 
comment out 
mirrorlist=http://mirrors.elrepo.org/mirrors-elrepo.el8

save and ```dnf makecache --refresh```
<More to come>
