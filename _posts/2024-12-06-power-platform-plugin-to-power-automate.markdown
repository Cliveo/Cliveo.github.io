---
layout: post
title:  "Power Platform Plugin - Power Automate with managed identity"
date:   2024-12-05 14:48:01 +1300
categories: Azure
---

I’m going to cover the steps I’ve taken to get Power Platform plugin packages working with Power Automate flows authenticating via managed identity.

This post is an extension of my previous blogs:

[Set up managed identity for Power Platform Plugins](/azure/2024/10/14/set-up-managed-identity-for-power-platform-plugins.html)

[Power Platform Plugin Package – Managed identity](/azure/2024/11/22/power-platform-plugin-package-managed-identity.html).

For information about creating the certificate, creating the managed identity, associating the identity to the package and assigning the plugin package's, refer to the above blogs.

You can find example code that I’ve provided on [github here](https://github.com/Cliveo/ManagedIdentityPlugin){:target="_blank"}

# Motivation
Power Automate offers an amazing suite of low code connections to various other systems, it's much easier to manage authentication & communication externally from Power Automate than it is from custom code in plugins. 

However one limitation with Power Automate is that it's asynchronous when triggered from Dataverse. 

Sometimes you need to be able to integrate in real time and this is why I'm sharing how to make your Power Automate trigger synchronously while still being secure!

# Step 1: Secure HTTP Flow to Managed Identity

In my example I'm making the Power Automate restricted to only `Specific users in my tenant`. 

Then I entered the `Object (principal) ID` from my managed identity in the `Allowed users` section.

The method & inputs can be whatever you require.

![here](/assets/plugin-power-automate/1.png)

![here](/assets/plugin-power-automate/2.png)

# Step 2: Add plugin logic

## Getting the token

To get the token required to call the flow you need to set the scope to be `https://service.flow.microsoft.com//.default`.

Note the `//` this is a subtle bit but required to make it work. Documentation on this can be found [here](https://learn.microsoft.com/en-us/entra/identity-platform/scopes-oidc#trailing-slash-and-default){:target="_blank"}

Getting the token is as easy as `var token = identityService.AcquireToken(scopes);`


## Calling the flow

Calling the flow just uses the standard `HttpClient` - `var client = new HttpClient();`

Adding authentication to the client requires the bearer token in the header `client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);`

Then calling the flow is just using the method required for your HTTP request.

As I'm using `GET` for my example the request looks like `var resp = client.GetAsync(flowUrl);`

Altogether the code looks like this:

![here](/assets/plugin-power-automate/3.png)

# Step 3: Build and deploy plugin

Building the package is as easy as `dotnet build --no-incremental`

![here](/assets/plugin-key-vault/4.png)

Then deploying I'm using `pac plugin push -id 62885d15-17a7-ef11-a72f-002248123766`

![here](/assets/plugin-key-vault/5.png)

# Step 4: Test the plugin & Flow


To demonstrate I'm just throwing an exception from the Flow on update of a record in Dataverse.

`throw new InvalidPluginExecutionException(resp.Result.Content.ReadAsStringAsync().Result);`

The flow successfully runs:

![here](/assets/plugin-power-automate/4.png)

The output from the Power Automate can then be used in the plugin in real time:

![here](/assets/plugin-power-automate/5.png)

# Conclusion

With only a couple of lines of code and a little bit of set up in Azure & Power Platform, you can securely connect your plugin packages to Power Automate.

I look forward to seeing how others use this! Please don't hesitate to reach out or connect with me on linkedin.