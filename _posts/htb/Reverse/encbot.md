---
layout: single
classes: #wide
title: "Hack The Box - Encryption Bot"
toc: true
date: 20224-03-19
toc_sticky: false
author_profile: true
collection: posts
categories: [reverse, Hack The Box]
tags: [ghidra, binary]
---
![title](/assets/images/encbot/title.png)
First reverse engineering challenge done!  


Now, this challenge wasn't possible without a little bit of help. Sadly, my
toolkit wasn't big enough without the help of a little google. Aproaching this
challenge I started with Ghidra in order to break everything apart. This was my
first time looking at assembly, or any insider code that I had never seen
before. After proding a little in ghidra, and using a few tutorials I began
bringing it all together. 


For this challenge, the very first thing I did was find out what the functions
did. I found, that the program first checks if the input is 27 characters. Then,
it converts the users input to binary. And finally converts the binary to chars
using a table.   

Now, came the hard part. Decrypting. This with the knowledge I had coming in was almost impossible. After trying for what felt like days (3 days to be exact). I folded and looked into the proper way to decrypt. Turns out, it was using functions that I didn't know existed! After analyzing the code that I found and how it worked. I was able to recreate what I had found online giving me the flag! Overall, this challege was fun and I would highly recommend.



## What I learned
* basics of ghidra
* how to convert chars to binary with python
    - `binary = ''.join(format(num, '06b') for num in index)`
* chars are 7 bits (note: can handle in 8 bit chunks)
* `[i:i+8]`
    - this code grabs 8 char sections
* `int("", 2)` 
    - converts binary to decimal 
* `chr()`
    - this fn converts decimal to ascii

## Conclusion
Yes, going into this challenge I didn't have the entire skill set that I needed. Nor, did I have any prior knowledge that you can convert from binary -> decimal -> ascii with python. Never once when I was trying to decrypt did it cross my mind (I was planning on attempting it manually). This challenge definitely pushed me out of my comfort zone. However, thats the best way to grow :)


Thanks for reading!  
-- Ryushe

