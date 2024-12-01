---
layout: post
title:  "Power Platform Plugin - Key vault with managed identity"
date:   2024-12-01 14:48:01 +1300
categories: Azure
---

I’m going to cover the steps I’ve taken to get Power Platform plugin packages working with key vault authenticating via managed identity.

This post is an extension of my previous blogs:

[Set up managed identity for Power Platform Plugins](/azure/2024/10/14/set-up-managed-identity-for-power-platform-plugins.html)

[Power Platform Plugin Package – Managed identity](/azure/2024/11/22/power-platform-plugin-package-managed-identity.html).

For information about creating the certificate, creating the managed identity, associating the identity to the package and assigning the plugin package's, refer to the above blogs.

You can find example code that I’ve provided on [github here](https://github.com/Cliveo/ManagedIdentityPlugin){:target="_blank"}

# Motivation
Over the years I have seen plenty of integrations orchestrated through plugins but typically securing credentials/keys used in the integration has not followed best practice. Managed identity is a big step to improving security for plugins that might need to retrieve secrets from key vault.

# Step 1: Give managed identity key vault permissions

In my example I am giving my managed identity `Key Vault Secrets User`, I'd recommend giving least privilege as possible in a production setting.

![here](/assets/plugin-key-vault/1.png)


As you can see here I have a vault called `ManagedIdentityPlugin`, with the single secret called `supersecret`.

![here](/assets/plugin-key-vault/2.png)

# Step 2: Add plugin logic to use SecretClient

I'm using the provided library `Azure.Security.KeyVault.Secrets`

The scopes required to get the token are `https://vault.azure.net/.default`

Using the provided `IManagedIdentityService` you can get a token.

The `TokenCredentialProvider` I created in the previous blog works with the `SecretClient`.

Now getting the secret is as easy as `client.GetSecret("supersecret").Value.Value`

![here](/assets/plugin-key-vault/3.png)

# Step 3: Build and deploy plugin

Building the package's as easy as `dotnet build --no-incremental`

![here](/assets/plugin-key-vault/4.png)

Then deploying I'm using `pac plugin push -id 62885d15-17a7-ef11-a72f-002248123766`

![here](/assets/plugin-key-vault/5.png)

# Step 4: Test the plugin

I've made the secret write as an exception to demonstrate:

![here](/assets/plugin-key-vault/6.png)

# Conclusion

With only a couple of lines of code and a little bit of set up in Azure & Power Platform, you can securely connect your plugin packages to Key Vault.

I look forward to seeing how others use this! Please don't hesitate to reach out or connect with me on linkedin.