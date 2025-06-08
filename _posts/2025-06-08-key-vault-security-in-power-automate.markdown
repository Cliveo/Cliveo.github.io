---
layout: post
title:  "How secure are your secrets in Power Automate?"
date:   2025-06-08 14:48:01 +1300
categories: Azure
---

Before we continue, please remember just because you can do something, doesn't mean you should. Act responsibly.

**"With great power comes great responsibility"** - Uncle Ben

# A common scenario

It's quite common to see Azure Key Vault used to "securely" store secrets that are needed for authentication of certain actions in Power Automate.

Obviously this is better than plain text hard coded values, or credentials being stored in insecure repositories, but I wonder how secure it really is?

In my example I have an automated flow, created by an administrator that has a connection and privileged to access Key Vault. 

With best intentions they seem to be doing everything right. They put the secret in Key Vault, they secured the outputs of get secret, they safely use it in their flow.

![Secure outputs enabled](/assets/secure-key-vault/secure-outputs.png)

Then comes user X, a system customizer, not privileged to the Key Vault. However because they are a system customizer they have enough privilege to modify the flow... they undo secure outputs on the get secret action and voila now they know the secret... not so secure.

![Insecure outputs revealing secrets](/assets/secure-key-vault/insecure-outputs.png)

User X thinks for a moment, if I can do this for one secret, maybe I can do this with more! They search through the actions available in Power Automate... List secrets! Eureka! 

They add list secrets, drop get secrets in a for each leveraging the list secrets output all using the admin's connection already provisioned for the flow. Uh oh... My entire vault has been compromised.

![List secrets action exposing all vault contents](/assets/secure-key-vault/list-secrets.png)

The same risk applies to any flow shared to a normal user, not just those with system customizer privileges - careful who you share with!

# Is this secure?

The short answer is: **Not as secure as you might think.**

While Azure Key Vault provides excellent security for storing secrets, its integration with Power Automate introduces unexpected attack vectors that many organizations overlook. The ability for users with flow modification privileges to bypass secure outputs and enumerate vault contents represents a significant security gap that could lead to widespread credential compromise.

The core issue isn't with Key Vault itself, but rather with how Power Automate's permission model interacts with Azure resources. When flows inherit administrative connections, they effectively grant vault access to anyone who can edit the flow - regardless of their actual Azure permissions.

# Potential Mitigations

## 1. Don't use Key Vault in flows
Logic Apps might be better perhaps? Permissions can be more granular using Azure access controls.

## 2. Implement DLP to restrict Key Vault connector use
Use Data Loss Prevention policies to restrict the use of Key Vault connector to specific users or environments.

## 3. Minimize users who have access to modify flows
- Reduce the number of system customizers
- Be very careful about who you share flows with
- Use least privilege principles

## 4. If you do use Key Vault in flows
- **Reduce scope in Key Vault's access policy** - only grant access to specific secrets needed, restrict list permissions.
- **Minimize the vault to be fit for purpose** - don't use a single vault for everything
- **Only store secrets used for the flows** - separate vault contents by purpose
- **Don't have one massive vault with all of your secrets!!** - this is the biggest risk

# Conclusion

While Azure Key Vault provides excellent security for storing secrets, its integration with Power Automate introduces unexpected attack vectors that many organizations overlook. The ability for users with flow modification privileges to bypass secure outputs and enumerate vault contents represents a significant security gap that could lead to widespread credential compromise.

The core issue isn't with Key Vault itself, but rather with how Power Automate's permission model interacts with Azure resources. When flows inherit administrative connections, they effectively grant vault access to anyone who can edit the flow - regardless of their actual Azure permissions.

Organizations must carefully evaluate whether the convenience of Key Vault integration in Power Automate justifies the expanded attack surface. For high-security environments, consider using Logic Apps with proper Azure RBAC, or implement strict governance around flow sharing and modification privileges.

Remember: **security is only as strong as its weakest link**, and in this case, that link might be the well-intentioned administrator who shared a flow with the wrong person.

I look forward to hearing your thoughts on this! Please don't hesitate to reach out or connect with me on LinkedIn.
