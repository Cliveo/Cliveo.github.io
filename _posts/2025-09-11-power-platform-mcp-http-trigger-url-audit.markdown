---
layout: post
title: "Power Platform MCP - Auditing HTTP / Teams Webhook Trigger URL Changes"
date: 2025-09-11 09:00:00 +1200
categories: Azure
description: Using my Dataverse + Power Automate MCP server to inventory flows, inspect triggers, and capture new HTTP callback URLs ahead of Microsoft’s 2025 retirement of logic.azure.com trigger endpoints.
image: /assets/posts/power-automate-triggers/Report.png
---

Microsoft have announced upcoming changes to HTTP and Teams Webhook trigger URLs in Power Automate. From **August 2025** new designer sessions will surface a new callback URL (no longer on `logic.azure.com`) and **after 30 November 2025** old URLs will stop working. Details: <https://learn.microsoft.com/en-us/troubleshoot/power-platform/power-automate/flow-run-issues/triggers-troubleshoot?tabs=new-designer#changes-to-http-or-teams-webhook-trigger-flows>

I wanted a fast, reproducible way to: inventory all flows in a solution, identify their trigger types, and capture any new manual HTTP trigger callback URLs so downstream callers can be updated before the cut‑off. Rather than a chain of ad‑hoc scripts, I used my Dataverse / Power Automate MCP server and a single natural language prompt.

## Goal

1. List every flow in a target solution.
2. For each, list trigger type(s).
3. Where the trigger is a manual HTTP trigger, fetch the (new) callback URL.
4. Capture everything in a markdown report for documentation & follow‑up.

## Prompt Used

Below is the exact prompt I issued to the MCP agent (replace values):

```
Get all flows for the solution '<SolutionName>' from 'https://<org>.crm6.dynamics.com', the region is <region>

1. Make a markdown file that has all the flows found in a table - add a column to show if the flow has been checked for step 2 & 3. The table should have the following columns:
   - Flow Name
   - Flow Id
   - Trigger Type
   - Trigger URL (if applicable)
   - Checked (Yes/No)
2. For each flow get the trigger, make sure to check every flow for its trigger and mark when that flows been completed.
3. If the flow is an manual trigger - get the trigger url and update the table with the trigger url, note not all manual triggers are http and some will error when trying to get the url, this is expected.
```

![Prompt issued to the MCP agent](/assets/posts/power-automate-triggers/Prompt.png)

## Environment Context Gathering

Before enumerating triggers the agent gathered essential environment data via the MCP server:

- Queried the solution record (name + id)
- Listed all flows cloud flows that belong to the solution
- Looked up the organization to obtain the `ProjectHostEnvironmentId` / environment Id required by the Power Automate API tools

This gave it all identifiers needed for subsequent trigger + callback operations.

![Agent context querying solution and flows](/assets/posts/power-automate-triggers/Context.png)

## Getting the Triggers

Dataverse / Power Automate endpoints exposed by the server:

- `get_flow_triggers` (enumerate triggers per flow)
- `get_manual_trigger_callback_url` (attempt callback retrieval when trigger is manual)

![Triggers listed for each flow](/assets/posts/power-automate-triggers/Triggers.png)

## Result

We have the report! It has done exactly what I have asked. 

![Generated markdown report preview](/assets/posts/power-automate-triggers/Report.png)

Notes produced by the agent:

- All flows processed for triggers (Step 2 complete).
- Manual trigger callback URL retrieval failed for non‑HTTP manual variants (expected – they are manual forms / button types, not raw HTTP).
- Only one manual HTTP trigger returned a URL (Managed Identity HTTP).

## Safety & Hygiene

Do not publish full callback URLs publicly, they contain shared access signatures.

## Prefer Scripting Over AI?

If you (or a reviewer) are not comfortable using an AI / MCP agent for this yet, you can achieve a similar outcome with a script-first approach:

- I previously published a PowerShell example that programmatically retrieves an HTTP trigger URL for a flow: [Power Automate - HTTP trigger URL](/azure/2025/01/21/power-automate-http-url.html). It embeds a gist with the core logic.

## Related

- Previous post: [Power Platform MCP – Open source Dataverse MCP server](/azure/2025/09/10/power-platform-mcp-open-source.html)


Feedback or ideas for next automation steps welcome in the repo: <https://github.com/Cliveo/Power-Platform-MCP>.
