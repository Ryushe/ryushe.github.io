---
layout: single
classes: #wide
title: "PortSwigger - Business Logic"
toc: true
toc_sticky: false
author_profile: true
collection: posts
categories: [portswigger]
tags: [burp]
---
Notes about this box: This box is a small simulation of what might exist out in the real world. So, even though some of these solutions may seem "easy", once introduced into a large complex environment are very practical to occur.

# Intro
I love logic flaws, something about being able to do something that was never supposed to be possible is very exciting to me.

Time to start :cold_face:

# Excessive trust in client-side controls
This section consisted of two labs:
1. shopping center that had a vulnerable post request 
    - challenge solution: editing the price of the post request to be < than a dollar -> this allows us to buy the product at the wrong price
    - remediation: dont include the price in the post request, you could solve this with a database that is queried
  
2. endpoint that didn't have brute force protection for the users 2fa
    - challenge sollution: send the request manually to the user -> brute force the 2fa code
    - remediation: implement lockout methods onto accounts when too many requests are in succession. 

# Failing to handle unconventional input
1. store front that allows us to add negative items into our cart
    - challenge solution: add the item you would like to buy into the cart, then add another item with a post request using the new items id and set the quantity to a negative value. Effectively lowering the carts cost.
        - Note: the cart could not be a negative value
    - remediation: use a nonsigned float value. This ensures that the value can no longer be negative. 
    - Alternative remediation: if value is < 0 remove the item from the cart
2. store front that improperly handles item count
    - challenge solution: using intruder or ffuf we can add a lot of items to our cart, eventually the overage of items makes the price go negative. 
        - Note: the cart could not be a negative value
    - remediations: limit the ammount of items that a user can hold in their cart, when the cart value hits the threshold amount don't let it increase anymore
  



