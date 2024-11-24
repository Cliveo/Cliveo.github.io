---
layout: post
title:  "Is something out of reach? PowerFX Traverse the Dataverse"
date:   2024-10-26 14:48:01 +1300
categories: Azure
---
<div class="frame"><iframe src="https://giphy.com/embed/fuwcnXOwS0myc" width="480" height="336" style="" frameborder="0" class="giphy-embed" allowfullscreen=""></iframe></div>


I’m going to share a simple tip that I’ve been finding very useful. Formula columns using PowerFX allow you to traverse the dataverse and pull information from several degrees of separation away.

Often there are tools available in power platform that require the data to be directly available on a table or at most, one hop away, this can be incredibly frustrating and quickly force the adoption of alternative solutions. These alternatives are usually either more complicated and sometimes costly.

# PowerFX can solve this problem! Here is my example:
You need to create a document or email to a contact, and provide them the information of how to contact their account manager.

The contact has an account (look up to an account)

The account has an account manager (look up to a contact)

Out of the box word documents & email templates can get information from the contact & the account, but cannot reach the account manager!

At this point you could decide I’m going to use power automate (a more complicated solution), or purchase a third party product (more costly).

Instead PowerFX can save the day!

# Step 1: Create the column
I create a column on contact “Account manager contact”.

1. It navigates to the contacts parent account
2. Drills into the primary contact
3. Retrieves their business phone number

![here](/assets/traverse-the-dataverse/fx-col.png)

# Step 2: Use it in a word template
Using the Formula column in a word template is easy, it’s just like any other attribute available in the XML mapping.


Here is the output:
![here](/assets/traverse-the-dataverse/word-template.png)


# Step 3: Create an email template
Just as before, the out of the box functionality to insert dynamic content now finds the column on contact. Add it where appropriate.

![here](/assets/traverse-the-dataverse/email-template.png)

Generate the email:
![here](/assets/traverse-the-dataverse/email-template-output.png)


# Conclusion
PowerFX can be a great tool that extends your reach across dataverse, its performant and simple.

Go forth and PowerFX!

# Disclaimer
This is a great solution when 99% of the out of the box templates are suitable and you only require a few additional columns. It is probably not the best solution if you find yourself adding dozens of FX columns to build your templates, at that point I’d be considering a more scalable solution.