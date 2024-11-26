---
layout: post
title:  "Power Platform Plugin Package – Managed identity"
date:   2024-11-22 14:48:01 +1300
categories: Azure
---

I’m going to cover the steps I’ve taken to get power platform plugin packages working with managed identity. Hopefully, this will help someone accelerate their success without facing the same challenges I encountered.

This post is an extension of my previous blog [Set up managed identity for Power Platform Plugins](/azure/2024/10/14/set-up-managed-identity-for-power-platform-plugins.html), so refer to it if you need details on how I create the certificate locally for use in my post-pack event.

You can find example code that I’ve provided on [github here](https://github.com/Cliveo/ManagedIdentityPlugin){:target="_blank"}

# Motivation
My primary goal was to enable the use of Azure libraries that provide clients to communicate with Azure resources. In my example, I’m using the `BlobServiceClient`.

# Step 1: Signing the package

My event requires a certificate called `ManagedIdentityPlugin` to be installed in the users personal certificates. (Check my previous blog [Set up managed identity for Power Platform Plugins](/azure/2024/10/14/set-up-managed-identity-for-power-platform-plugins.html) if you need help doing this)

Here’s the process:

I had to download that certificate & install it in Trusted Root Certificate Authority (this is for `nuget sign` to work below)

This is to prevent getting the error:

`NU3018: UntrustedRoot: A certificate chain processed, but terminated in a root certificate which is not trusted by the trust provider.`

Find the certificate in Personal certificates, export, follow the wizard to the end using default options.

![here](/assets/plugin-package/1.png)

Open the exported certificate and select Install.

![here](/assets/plugin-package/2.png)

It should end up looking like this:

![here](/assets/plugin-package/3.png)

In my example code you will find that I’ve added a post pack event

![here](/assets/plugin-package/4.png)

The main differences with this are instead of using `signtool` I’m using `dotnet nuget sign` (I found it wouldn’t sign the `nupkg` only the `.dll`).

`dotnet nuget sign $(ProjectDir)bin\$(ConfigurationName)\ManagedIdentityPlugin.1.0.0.nupkg --certificate-subject-name ManagedIdentityPlugin --overwrite --timestamper http://timestamp.digicert.com`

The other main thing to take note of is I’ve placed it in the after pack event `AfterTargets="Pack"`
This is because the nuget package needs to be packed before I can sign it, purely using post build event was not sufficient.

# Step 2: Token to TokenCredential
Another hurdle was the azure client libraries expect one of these classes that provide a `GetToken` method, but the Microsoft `IManagedIdentityService` interface has `AcquireToken` which `returns` a `string` not an `AccessToken` object.

I created a class that seems to work (**if there is a better way please someone tell me!**) 

It simply takes the `token` you get from `IManagedIdentityService`, extends `TokenCredential` abstract class so you can pass it to `BlobServiceClient` constructor.

![here](/assets/plugin-package/5.png)

![here](/assets/plugin-package/6.png)

# Step 3: Building the package
Building the plugin package is as easy as `dotnet build`

![here](/assets/plugin-package/build.png)


# Step 4: Getting the package to register
I had lots of issues adding `Azure.Storage.Blobs` to my package. The main one was I kept getting errors like:

`'Method not found: ‘System.BinaryData System.ClientModel.Primitives.IPersistableModel'`

Eventually I found adding the package `System.ClientModel` directly seemed to resolve the issue.

My installed packages look like this:

![here](/assets/plugin-package/7.png)

I registered the package with the `Plugin Registration Tool`.

# Step 5: Associate the managed identity to the plugin package
Just as before, once you’ve registered your `package` you need to associate it to the `managed identity` in Dataverse, the main difference here is you are **not** associating it to the `plugin`. 

Request
``` 
PATCH
https://<orgURL>/api/data/v9.0/pluginpackages(<PackageId>)
```

Body
```
{
    "managedidentityid@odata.bind": "/managedidentities(f712c637-ab8e-4f60-b740-110c10b22c1a)"
}
```

Here is an example request:

![here](/assets/plugin-package/8.png)

# Conclusion:
Once you’ve done this you should have everything you need to get the best out of the microsoft provided azure libraries, securely connected to azure from Dataverse within a plugin package!