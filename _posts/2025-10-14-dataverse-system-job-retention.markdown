---
layout: post
title: "Are You Cleaning Up Your Dataverse System Jobs?"
date: 2025-10-14 09:00:00 +1200
categories: Azure
description: Dataverse system jobs accumulate quickly and can impact performance and storage. Here's how to enable automatic cleanup with configurable retention periods.
image: /assets/posts/system-job-retention/cleanup.png
---

Every workflow execution, plug-in run, and bulk operation in Dataverse creates a system job record in the AsyncOperation table. These completed jobs pile up over time, consuming database storage and degrading performance. Despite Microsoft providing built-in cleanup functionality, many organizations aren't using it.

## The Built-In Solution

Dataverse includes an automatic deletion service with configurable retention periods. Defaults are 30 days for succeeded jobs and 60 days for failed or canceled jobs.

Enable it in the Power Platform admin center under **Audit and logs** → **Set retention policy for system job deletions**. Check the box and adjust the retention periods to match your needs.

## Managing with MCP

I've added system job retention management to my [Power Platform MCP server](https://github.com/Cliveo/Power-Platform-MCP){:target="_blank"}. Configure retention policies conversationally through an AI assistant instead of clicking through admin portals. This is particularly useful for standardizing settings across multiple environments.

**Note:** Microsoft added the admin portal UI right after I built the MCP tool. Classic timing. The tool still helps for automation and multi-environment management.

## The Question

**Are you using system job retention in your environments?** If so, what retention periods work for you? Have you noticed performance improvements?

The feature is [documented on Microsoft Learn](https://learn.microsoft.com/en-us/power-platform/admin/cleanup-asyncoperationbase-table){:target="_blank"}.
## Related Posts

- [Power Platform MCP - Open source Dataverse MCP server]({{ '/azure/2025/09/09/power-platform-mcp-open-source.html' | relative_url }})
- [Power Platform MCP - Investigating Power Automate Flow Runs with AI]({{ '/azure/2025/09/17/power-platform-mcp-flow-run-investigation.html' | relative_url }})
