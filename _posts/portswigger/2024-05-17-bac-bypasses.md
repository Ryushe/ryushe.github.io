---
layout: single
classes: #wide
title: "PortSwigger - Broken Access Controls"
toc: true
toc_sticky: false
author_profile: true
collection: posts
categories: [portswigger]
tags: [learning]
---

Embarking on a journey to learn more about the OWASP top 10 vulnerabilites led me to the PortSwigger Broken Access Controls labs. For the sake of time I am not going to go super indepth upon these shorter labs, but touch base and explain what I learned. 


# Introduction To BAC's
I had no idea what were bad access controls entering these labs, so we were starting from the beginning.

# #1 Access Controls Can Be Circumvented
The first lab we are presented with is teaching us about how to circumvent basic access controls. This is done within this lab by a publicly disclosed admin endpoint with no access controls. Navigating to the shown endpoint in /robots.txt gets us into the admin panel. 


# #2 Unprotected Admin Endpoint (non predictable endpoint name)
Again, this endpoint was publicly disclosed within the app (shown in one of the .js files). After navigating to this custom endpoint we have admin access.


# #3/4 User Role Controlled by Request perameter & Role Can be Modified
For the first lab, we are able to change our admin status by editing the url from https://site.com to https://site.com?admin=true. Thus, giving us admin status.

The second lab was similar to the first allowing us to change our role by adding ?role=1 to the end of the url endpoint. 

These issues can be mitigated by properly sanitizing the url's that are inputted and not directly using client side verification.


# #5 Platform Misconfigurations
So the site uses primarily front end access controls. What harm could this cause... Well, if it supports various non-standard HTTP headers `X-Original-URL` and `X-Rewrite-URL` may be supported. If these headers are accepted it may be possible to overwrite the original url. Meaning, we can send the POST request to a different endpoint that will allow it go through the access controls. Then, the url is changed by the `X-` header. And BOOM, the request is sent :fire: 

# #6 Method Changes.... AKA HTTP Method Tampering
POST, GET, PUT, DELETE. All of these are different types of http requests you
are allowed to make. Do all of these have the same restrictions? Can we change a
GET -> PUT and have it execute as a different type?

For the lab, it was as simple as changing the POST -> GET, and the server
executes it as a POST request. Why? We have 3 options; server misconfiguration
executing GET as POST, Lack of input validation allowing for the modification of
the request, or the library that we are using has a vulnerability. 

# #7 IDORS!
Users have user ids. What if that were changed, what if that id is leaked? Well,
these would lead to idors. IDOR is when a user is able to change a variable in
the request and access otherwise unacessible data. 

In the lab, we are able to change the ID of our user to carlos. Thus giving us access to carlos's account.

# #8,9,10,11 More IDORS...
#8 - In the comments, the user's ids are leaked. Entering in carlos's id within the url such as `?id=carlosid` gives us full access to the account.

#9 - Changing our id to carlos we see that we are redirected back to the login page. Upon inspecting the redirect path that the application uses, it leaks the page within the request and then redirects to the login page. BINGO, credentials 

#10 - Changing our id to `administrator` shows us the admin password... Juicy ik 

#11 - Chatroom that allows you to download transcripts, what could go wrong? Well, when you download the file you see that it is 2.txt. Wait.. what happened to 1? Well making the same request to 1.txt gives us all of the information from the previous user's chat room :smiling_imp:

# #12 Step Missing AC's
Upon browsing the site we see multiple requests to the admin panel when you change a users permission level. Are all of the endpoints secured the same? In this case no, we are able to repeat one of the admin requests using our normal user account's session id to change our own account to an admin. 

This works because not all of the steps to change the users permissions had access controls :smile:

# #13 Referer Misconfiguration
/admin, a sensitive endpoint on this lab. When we send a request to the /admin/delete user the application checks to see if the /admin is in the referal header. And if it is, it executes the request as normal. Well, we aren't checking to see who the user is and if they have proper permissions... Meaning, any user can send that request with the /admin referer and have access to anything admin related...



# Learning

Where to start, there are so many places that the server/access controls may be misconfigured. From initial login steps, to sensitive endpoints not checking who the users are.

These were some simple examples that happen frequently in everyday companies. In-fact this type of vulnerability is one of the most common vulnerabilities to occur.

How can we prevent against this?  
Ensuring that we have proper input sanitization and check who the user is, and what permissions the user has at every step. Also, hardening our server to not accept headers such as `X-Original-URL`. 




The end,   
Thank you for reading!