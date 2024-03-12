---
layout: single
classes: #wide
title: "TwoMillion"
toc: true
toc_sticky: false
author_profile: true
collection: posts
categories: [sherlock, htb]
tags: [logs, sqlite]
---

# BumbleBee
![Title](/assets/images/bumblebee/title.png)
A little context about this box, this box is a DFIR aka digital forensics and
incident response.  

The scenario: "An external contractor has accessed the internal forum here at Forela via the Guest WiFi and they appear to have stolen credentials for the administrative user! We have attached some logs from the forum and a full database dump in sqlite3 format to help you in your investigation."  

Upon downloading the required zip and extracting it you are
presented with the fiels access.log and phpbb.sqlite3. The access.log really
only contains the ip of the contractor and the user-agent he used during the
requests. Not amazing, but some information non the less.

K, starting off q's 1 & 2:  
"What was the username of the external contractor?"  
This information can be found in any of the information, personally I found it
in the phpbb_users section of the sql database. We know what the users ip is,
due to it being littered throughout the log file we were given
`10.10.0.78`(answer to q2). Using this we can look through the user db and see
that the username of the contractor is apoole1. For the ip address we obtained
we can double check that it is correct by checking the phpbb_log file within the
db.   

q3:   
"What is the post_id of the malicious post that the contractor made?"  
This can be found by looking at the posts section of the db. We know that the
contractor's ip is `10.10.0.78`, so we can correlate that the post from
`10.10.0.78` is the contractor. 

![postid](/assets/images/bumblebee/postid.png)

q4:   
"What is the full URI that the credential stealer sends its data to?"  
While still in the posts area of the db, we can now look at the malicious post
that the contractor made. In this case looking through the post we can see that
the form action, which is what will be executed when the user clicks the button
is sending the info to: `http://10.10.0.78/update.php`

![badposturl](/assets/images/bumblebee/badposturl.png)

q5:  
"When did the contractor log into the forum as the administrator? (UTC)"  
Checking the main log file again from the db, we can see that the time that the
contractor logged into the form was: `1682506392`. Now, this time isn't in the
proper format like the question asks. So, to convert `1682506392` -> UTC in our
shell we can use `date -d @1682506392 -u` to convert to the correct time. 

![adminlogin](/assets/images/bumblebee/adminlogin.png)
![dateconv](/assets/images/bumblebee/dateconv.png)

q6:   
"In the forum there are plaintext credentials for the LDAP connection, what is the password?"  
Being honest, this one took me the longest to understand. This is because when
you refer to the forum there is nothing about a LDAP login. However, checking
the config will give a completely different result. Filtering the results in
`phpbb_config` will show you:

![ldappw](/assets/images/bumblebee/ldappw.png)

q7:  
"What is the user agent of the Administrator user?"  
This question was as simple as looking at the log file. This is because logs
usually include ips and user agents from the user that made the request. I'm not
going to spoil this one :)

q8:  
"What time did the contractor add themselves to the Administrator group? (UTC)"  
This question is roughly the same as the other time question. Looking at the log file once again will show you the time in epoch format (a unix timestamp). So, converting from the unix time will give you q8. 

q9:   
"What time did the contractor download the database backup? (UTC)"  
This answer is found in the log file. The answer is near the bottom, and a `GET`
request was preformed. The time format of this answer is +0100 to start (which
is 1 hour ahead of UTC). So, subtracting an hour will result in the answer.


q10:  
"What was the size in bytes of the database backup as stated by access.log?"   
This answer is found right next to the previous answer. When the `GET` request is made next to the 200 status shows the size of the file download. 



Conclusion:   
Personally, I really like these kinds of problems. I can tell that blogging
isn't quite my thing. I would much rather grind out a bunch of problems. So, if
not all of my journey is documented that's why. Now, wrapping up what I learned.
I learned how to properly go through a sql db, and write a little bit of syntax.
Personally, I prefer to use the gui when looking at db's. Due to its human
friendly nature. This challenge also helped me not be so scared of log files.
Everything within a log file is there for a reason. So, take your time and
anaylze as best you can. 

THANK YOU SO MUCH FOR READING  

Ryushe out

![peace](/assets/images/pudgiepeace.gif)





