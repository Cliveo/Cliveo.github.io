---
layout: post
title:  "Power platform - Bulk activating workflows with PowerShell"
date:   2024-11-11 14:48:01 +1300
categories: Azure
---
Bulk activating workflows in power platform can be painful. I'm going to share a simple snippet I use quite often. Sometimes things go wrong, pipelines fail or for whatever reason you have a bunch of processes, classic workflows or power automate that are all off.

The maker portal only allows you to activate one at a time, it can be tedious and time consuming. For some reason It always happens at the worst time.

With this script you simply provide a `solution` & `environment URL` and it will prompt you for `login`, then loop through activating either `cloud flows` or `workflows`, whichever you choose.

The script has a dependency on [`Microsoft.Xrm.Data.PowerShell`](https://github.com/seanmcne/Microsoft.Xrm.Data.PowerShell){:target="_blank"}

So if you don't already have it simply install running the following:

`Install-Module Microsoft.Xrm.Data.PowerShell -Scope CurrentUser`

<br/>

{% gist b180f3002740f8dd7b9e8c313b81af61 %}