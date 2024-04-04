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

# Google Dorking

Go to last page and get the omitted reuslts

| Filter          | Description                                        | Example                              |
| :-------------- |:---------------------------------------------------| :------------------------------------|
| allintext      | Searches for occurrences of all the keywords given. | `allintext:"keyword"` |
| intext      | Searches for the occurrences of keywords all at once or one at a time. | `intext:"keyword"` |
| inurl      | Searches for a URL matching one of the keywords. | `inurl:"keyword"` |
| allinurl      | Searches for a URL matching all the keywords in the query. | `allinurl:"keyword"` |
| intitle      | Searches for occurrences of keywords in title all or one. | `intitle:"keyword"` |
| allintitle      | Searches for occurrences of keywords all at a time. | `allintitle:"keyword"` |
| site      | Specifically searches that particular site and lists all the results for that site. | `site:"www.google.com"` |
| filetype      | Searches for a particular filetype mentioned in the query. | `filetype:"pdf"` |
| link      | Searches for external links to pages. | `link:"keyword"` |
| numrange      | Used to locate specific numbers in your searches. | `numrange:321-325` |
| before/after      | Used to search within a particular date range. | `filetype:pdf & (before:2000-01-01 after:2001-01-01)` |
| allinanchor (and also inanchor)      | This shows sites which have the keyterms in links pointing to them, in order of the most links. | `inanchor:rat` |
| allinpostauthor (and also inpostauthor)      | Exclusive to blog search, this one picks out blog posts that are written by specific individuals. | `allinpostauthor:"keyword"` |
| related      | List web pages that are “similar” to a specified web page. | `related:www.google.com` |
| cache      | Shows the version of the web page that Google has in its cache. | `cache:www.google.com` |

## Operators
& | () * ~   
- ~ - bring back synonyms for hte term
    - ex: ~set -> configure, collection, change

include `+site:`  
exclude `-site:`

# Github Dorking
List [here](https://book.hacktricks.xyz/generic-methodologies-and-resources/external-recon-methodology/github-leaked-secrets)  

| Filter | Common Attributes | Description |
|---|---|---|
| "Company" stage | staging, stg, dev, prod, qa, swagger | Have devs pushed bad code? |
| "Company" apiKey | apiSecre, x-api-key, apidocs, /api/, /internal/api | Exposed API keys? What are they used for? (Look at docs)  ex: client_id= always same and github provided key to bypass 2fa | |
| "Company" keyword | login, signup, registration, admin, administrator, appspot, firebase, password, testuser, testing | Looking for specific features | |

# Wordlists
seclists - /usr/share/wordlists/seclists  
[onelistforall](https://github.com/six2dez/OneListForAll) 

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
