---
layout: post
title:  "Power Platform - Power Automate - HTTP trigger URL"
date:   2025-01-19 14:48:01 +1300
categories: Azure
---

Have you ever wondered if it was possible to programmatically get the HTTP URL of an HTTP triggered Power Automate? So have I!

{:refdef: style="text-align: center;"}
![here](/assets/flow-url/2.png)
{: refdef}

# Motivation

When deploying HTTP triggered Power Automate flows you are only able to get the URL of the flow after it has been deployed into the environment.

I was wondering if I might be able to get the URL using `PowerShell`, so that I can then pass that onto anything that might need it, environment variables, application settings etc.

# Gist

If at any point someone finds a supported & documented API/CLI command to be able to do this. Please share!

```
Note: You might need to change the flow endpoint to your geography
```

The script has a dependency on [az PowerShell module](https://learn.microsoft.com/en-us/powershell/azure/install-azps-windows?view=azps-13.1.0&tabs=powershell&pivots=windows-psgallery#installation){:target="_blank"}

{% gist 4997d28fea4ff12693bed167522cab08 %}


If my flow wasn't restricted to specific users the SAS token would be in the query string as well.

The response also includes the method `GET`, `POST`, etc.

And here it is an action! 

![here](/assets/flow-url/1.png)



