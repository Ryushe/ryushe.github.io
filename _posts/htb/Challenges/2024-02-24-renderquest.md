---
layout: single
classes: #wide
title: "Hack The Box - Render Quest"
toc: true
toc_sticky: false
author_profile: true
collection: posts
categories: [CTF, Hack The Box]
tags: [burp, web injection, go]
---
![title](/assets/images/renderquest/titleImage.png)

<b>Challenge Description</b>:  
You've found a website that lets you input remote templates for rendering. Your task is to exploit this system's vulnerabilities to access and retrieve a hidden flag. Good luck!  

Going into this challenge I had never done 
any of the challenges page. I wanted to see how these were 
different compared to the machine tab. The main difference I saw
between this format and the machines, is you are not connected
directly to the machine. This critical factor posed to be an issue
throughout this box due to my not knowing how to expose my machine
corectly.  

Upon starting this challenge (post install and extraction of the zip)
, you are presented with the layout of the page. Because there was 
an input box I tried all sorts of things from XSS to SQLi. This resulted
in an error message each time. 

# Input Validation Error

Next, I looked over the source code a little further and to my surprise
there was a big input validation error.

![badFN](/assets/images/renderquest/badFunction.png)  

As you can see in the image above, there is no input validation here.
When the server fetches the info, it sends whatever what in that
request directly to the server. This means, when you click the render
button it takes anything in the file, reads it and executes it as a 
shell command.

Now, you might be thinking "It doesn't matter if you can execute it
if the code is never read". This would be true, however on line 126
it allows us to manually set the remote value to true in the url.

![manualRemote](/assets/images/renderquest/manualRemoteSet.png)  

![ifTrue](/assets/images/renderquest/ifRemoteTrue.png)  

So, if use_remote is set to true in the url it will read the remote file
(eg: the template) that is provided to the server.   

Example url: http://83.136.253.251:49423/render?use_remote=true&page=https://webhook.site/2865e172-7114-494e-a961-0859118ac2a8



# Exploitation
Now that we know that there is poor input handling and the 
template we provide will be read we are able to 
take advantage of it. Due to us not having direct connection to the 
machine vpn we have 2 choices. Create a webhook or expose our
own machines ip and port to send the mallicious file. Deciding to go 
with the first option I create a payload using webhook.site and it
looks like this:

![webhook](/assets/images/renderquest/webhook.png)  

With a result of:

![output1](/assets/images/renderquest/output1.png)  

Now all that's needed is to change the payload to get the flag.  

Hehe no spoilers XD

# Explanation Of Why It Works
Im adding this part in so that we can learn together and know what not
to do in order to prevent things like this happening on our own 
networks.  
<b>Reason it worked:</b>
* No input validation when handling user input
* Go hosting a webserver using templates vulnerability 
    - Vulnerability can be found [here](https://docs.fluidattacks.com/criteria/fixes/go/422/)
* Function directly executes bash commands  

<b>How to mitigate:</b>
* Add input validation
* Check for go patches, or get rid of templates all together if no longer needed
* Don't directly execute bash commands within your unsanitzied code