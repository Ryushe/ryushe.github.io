---
layout: single
classes: #wide
title: "ZSeano's Xss Challenge #1"
toc: true
toc_sticky: false
author_profile: true
collection: posts
categories: [etc]
tags: [xss, regex, static]
---
![title](/assets/images/etc/zseano.png)

About this challenge: This challenge is hosted on ZSeano's website bugbountyhunter. These are very practical hands on training that allow you to see the source code. In this instance we had a controllable variable called clk that had very little sanitization.

# initial perameters
```js
var cfpPid = cfpAlphaParam("pid", 0);
var url = "https://www.bugbountyhunter.com/";
var cfpClick = cfpParam("clk");
var cfpOrd = cfpParam("n");
if (cfpOrd === "") {
  var axel = Math.random() + "";
  cfpOrd = axel * 1000000000000000000;
}
```

The code above is good because we have two perameters clk and n that can be
controlled by adding ? and then the variable name to assign values to it.

# Regex
Looking further we see the sanitizing function (shown below)
```js
function cfpParam(name) {
  var regex = new RegExp("[#]" + name + "=([^\\?&#]*)"); // match for #name=data
  var t = window.location.href;
  var loc = t.replace(/%23/g, "#");
  var results = regex.exec(loc);
  return (results === null) ? "" : unescape(results[1]); // get data
}
```
This code will check for a literal #, then use the name passed (in this case
clk) and then check to for any of the characters `=([^\\?&#]*)`.  

Breakdown: This regex `=([^\\?&#]*)` means that it will accept anything after a = sign and stop capturing the input to send it if it finds a ?&# symbol.

Now in this case %23 is translated into # so we can make payloads like this
`%23clk=message` and have message displayed. This option would not be possible
if %23 wasn't converted because `#` in a url points to css.

In this instance, if we find a value that matches that criteria we pass whatever was on the left side of the = sign
example:
```
%23clk=message
returns: message
```

Now that we know that we have a location that we can control the input and write to the final url its time to take advantage. Within this challege the end payload will execute the final code as seen below. 
```js
document.write("<script src='" + pr_s + "'><\/script>");
```

# Creating the Payload
At first the " character made me think that I needed to escape fully with something like '", however this was not the case... We are concatinating the values meaning that when the final url is constructed they don't matter.

So, what do we need to do? 
1. escape the ' character in src so we can execute our own payload
  - NOTE: we could add a x before our single quote to ensure that the url is invalidated and call something like the onerror method
2. close the > of the script tag
3. end the script tag
4. create our own script tag
5. put our payload within our tag

NOTES:
- steps 4-5 aren't necessarily needed, however it allows us a little more control IMO

# Solution
So, putting it all together we get
`https://www.bugbountytraining.com/challenges/challenge-8.php?#clk='></script><script>alert(1)</script><script>` 


# Learning
Just recently, I've taken an interest in reading code statically. Especially Javascript files. Something about being able to reconstruct another developer's thoughts is very interesting to me.

What I learned/strengthend: 
- Static code analysis
- Exp reading regex
- what # does in a url (never thought about it until now)
