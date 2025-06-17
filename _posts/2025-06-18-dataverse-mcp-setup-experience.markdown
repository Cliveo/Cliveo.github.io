---
layout: post
title:  "Setting up Dataverse MCP: Lessons Learned"
date:   2025-06-17 10:30:00 +1300
categories: Azure
---

I'd like to share my experience setting up Dataverse MCP (Model Context Protocol). 

[Daniel](https://www.linkedin.com/in/laskewitz/) has done an amazing job providing some of the best content to quickly and easily get set up using Dataverse MCP.

Check out the labs available here: [https://github.com/microsoft/Dataverse-MCP](https://github.com/microsoft/Dataverse-MCP){:target="_blank"}

I have to admit my first mistake was that I had a `man look` and skimmed over the first two steps 😅

So my first bit of advice is: **follow the documentation step by step - the devil is in the detail**.

# No Tools Available

My first assumption was that I already had an environment in this location. Turns out it's quite important, as this is the only region where MCP is available.

My mistake was that I had an environment in US with early release features enabled:

![Early release features enabled](/assets/mcp-setup/early-release.png)

However, this is different from the first release region:

![First release location selection](/assets/mcp-setup/first-released-location.png)

More details can be found here: [Dataverse MCP Issue #5](https://github.com/microsoft/Dataverse-MCP/issues/5){:target="_blank"}

## Trial License Blocker

Something that presented a small blocker: when I discovered my mistake, I had no `trial licenses available in my tenant`. I received an error when trying the following:

```PowerShell
New-AdminPowerAppEnvironment -DisplayName '<friendly name>' -Location unitedstatesfirstrelease -EnvironmentSku Trial -ProvisionDatabase
```

A small adjustment can be made so that you can leverage `developer` environments instead:

```PowerShell
New-AdminPowerAppEnvironment -DisplayName '<friendly name>' -Location unitedstatesfirstrelease -EnvironmentSku Developer -ProvisionDatabase
```

## Environment Creation Error Red Herring

Something to be aware of the above command can often error with the following:

```PowerShell
InvokeApiNoParseContent: Cannot process argument transformation on parameter 'Route'. Cannot convert value to type System.String.
```

This is a red herring - for me at least, the environment was always created despite the error.

Check [https://admin.powerplatform.microsoft.com/manage/environments](https://admin.powerplatform.microsoft.com/manage/environments){:target="_blank"} to verify if it actually got created.

More details can be found at: [Dataverse MCP Issue #9](https://github.com/microsoft/Dataverse-MCP/issues/9){:target="_blank"}

# Getting the Connection - URL Matters!

I've created many Dataverse connections in my time, so naturally I didn't follow the link provided and navigated to [https://make.powerapps.com](https://make.powerapps.com){:target="_blank"} to create the connection.

It took me an hour to realize that the link provided in the documentation takes you to [https://make.preview.powerautomate.com](https://make.preview.powerautomate.com){:target="_blank"}

The URL of the connection ends up being quite different:

## Doesn't Work
```
https://make.powerapps.com/environments/.../connections/shared_commondataserviceforapps/.../details
```

## Works
```
https://make.preview.powerautomate.com/environments/.../connections?apiName=shared_commondataserviceforapps&connectionName=...
```

# 3. Pro Tip: Turn Up the Log Level

When trying to get it to work in VS Code, something that can be quite useful is turning up the log level:

Open the command palette: `Ctrl + Shift + P`

Select: `Developer: Set log level`

![Set log level](/assets/mcp-setup/set-log-level.png)

Select: `Trace`

![Trace log level](/assets/mcp-setup/trace-log-level.png)

Now the traces are still quite hard to decipher, but I'm confident that with time they will get much better and the community will start having references to common issues and their resolutions.

# 4. Usage and Exciting Possibilities

Now I'm really excited to share all of the ways this can be used to enhance your `AI-assisted development`.

One thing that I've already found quite intriguing is when writing short scripts for things like pipelines in `PowerShell` - the ability to give your agent context about your environment metadata.

This is similar to when we use tools like the `early bound generator` to give our `IDE context` of the `entities` and `columns` available.

More on this in my next post!

# Summary

The key takeaway is that while there are some setup gotchas, once you get past them, Dataverse MCP opens up incredible possibilities for AI-enhanced Power Platform development. Stay tuned for more posts on practical applications!
