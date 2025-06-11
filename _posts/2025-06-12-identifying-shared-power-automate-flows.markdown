---
layout: post
title:  "Identifying Shared Power Automate Flows: A Security Audit Essential"
date:   2025-06-11 09:30:01 +1300
categories: Azure
---

Following on from my [previous post about Key Vault security risks in Power Automate](/azure/2025/06/08/key-vault-security-in-power-automate.html), I've received several questions about how to identify which flows have been shared and with whom. This is crucial for security auditing, especially when considering the risks I outlined earlier.

# Why This Matters

As we discovered, shared flows can expose significant security vulnerabilities. When flows inherit administrative connections (like Key Vault access), anyone with edit permissions effectively gains those privileges - regardless of their actual Azure permissions.

**The key question**: *Who has access to modify your flows?*

# Finding Shared Flows with SQL4CDS

Using the excellent [SQL4CDS plugin](https://github.com/MarkMpn/Sql4Cds) in XrmToolbox, we can query Dataverse directly to identify shared flows and their permissions.

## Query 1: Flows Shared with Individual Users

```sql
SELECT u.domainname, w.name, * 
FROM dbo.principalobjectaccess p
JOIN workflow w ON objectid = workflowid
JOIN systemuser u ON principalid = systemuserid
WHERE w.category = 5 --cloud flow
```

This query returns:
- **domainname**: The user's domain account
- **name**: The flow name
- All permission details from the principalobjectaccess table

## Query 2: Flows Shared with Teams

```sql
SELECT t.name, w.name, * 
FROM dbo.principalobjectaccess p
JOIN workflow w ON objectid = workflowid
JOIN team t ON p.principalid = t.teamid
WHERE w.category = 5 --cloud flow
```

This query shows:
- **t.name**: The team name
- **w.name**: The flow name
- All permission details from the principalobjectaccess table


# What to Look For

When reviewing the results, pay special attention to:

1. **Flows shared with large teams** - Broader attack surface
2. **Unexpected sharing patterns** - Users who shouldn't have access

# Taking Action

Once you've identified problematic sharing:

1. **Review necessity** - Does this user/team really need access?
2. **Check flow contents** - What sensitive connections does it use?
3. **Implement principle of least privilege** - Remove unnecessary shares

# Conclusion

Visibility is the first step toward security. These simple SQL queries give you the insight needed to understand your Power Automate sharing landscape and identify potential security risks before they become breaches.

Remember: **You can't secure what you can't see.**

---

*Have questions about Power Platform security? Connect with me on [LinkedIn](https://www.linkedin.com/in/cliveoldridge) - I'd love to hear about your experiences!*
