---
layout: single
classes: #wide
title: "Recon"
sidebar:
    title: "Recon"
    nav: infosec
collection: infopages
permalink: /infosec/recon/
toc: true
toc_sticky: true
author_profile: true
---

# lostsec methodology
1. `subfinder -dL urls.txt -all -recursive -o new-subdomains.txt`
1. `curl -s https://crt.sh/\?q\=\navan.com\&output\=json | jq -r '.[].name_value' | grep -Po '(\w+\.\w+\.\w+)$' | anew new-subdomains.txt`
1. `cat new-subdomains.txt | httpx-toolkit -l new-subdomains.txt -ports 443,80,8080,8000,8888 -threads 200 > subdomains_alive.txt`
    - httpx-toolkit - crawling/scraping, benchmarking, web app testing, api testing and more
1. `naabu -list new-subdomains.txt -c 50 -nmap-cli 'nmap -sV -sC' -o naabu-full.txt`
    - naabu - port scanner and subdomain enumeration tool
1. `dirsearch -l subdomains_alive.txt -x 600,502,439,404,400 -R 5 --random-agent -t 100 -F -o directory.txt -w ~/OneListForAll/onelistforallshort.txt`
    - ik what it does lol
1. `cat subdomains_alive.txt | gau > params.txt`
    - gau (get all urls) fetches urls form alienvaults open threat exchange
1. `cat params.txt | uro -o filterparam.txt`
    - uro - declutters urls
1. `cat filterparam.txt | grep ".js$" > jsfiles.txt`
    - greps for .js
1. `cat jsfiles.txt | uro | anew jsfiles.txt`
    - anew - appends new files
1. `cat jsfiles.txt | while read url; do python3 ~/tools/SecretFinder/SecretFinder.py -i $url -o cli >> secret.txt; done`
    - for url run secretfinder
1. `nuclei -list filterparam.txt -c 70 -rl 200 -fhr -lfa -t -o nucleinavan.txt -es info`
    - nuclei - sends requests accross argets based on template

## Tools & links 
- [gau](https://github.com/lc/gau)
- [uro](https://github.com/s0md3v/uro)
- [subfinder](https://github.com/projectdiscovery/subfinder)
- [secretfinder](https://github.com/m4ll0k/SecretFinder)
- [nuclei](https://github.com/CharanRayudu/Custom-Nuclei-Templates.git) custom templates

# another methodology
![lol](https://pbs.twimg.com/media/GJudL27WUAM16SB?format=jpg&name=small)