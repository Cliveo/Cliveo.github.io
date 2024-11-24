---
layout: post
title:  "Power Automate – Custom Connector C# – Remove special characters"
date:   2024-11-17 14:48:01 +1300
categories: Azure
---

A common requirement is the ability to remove special characters from a string. Power automate has some basic string expressions, but to remove a set of special characters from any given string is harder than it should be.

# Common solutions:
A big expression with nested replace (less actions but hard to read)

A loop within power automate (lots of actions & not scalable)

# My solution – Custom connector with C# code
Microsoft documentation can be found [here](https://learn.microsoft.com/en-us/connectors/custom-connectors/write-code){:target="_blank"}

My code can be found here at [github](https://github.com/Cliveo/CustomConnector-RemoveSpecialCharacters/tree/main/CustomConnectorTests){:target="_blank"}

Watch the video for a demo of how to set it up:
<iframe class="youtube" src="https://www.youtube.com/embed/hpeT0FGKIOo?si=sN3iypm_bfFQRdan" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>