---
layout: post
title: "Power Platform MCP - Investigating Power Automate Flow Runs with AI"
date: 2025-09-17 10:00:00 +1200
categories: Azure
description: Using my Dataverse + Power Automate MCP server to investigate failed flow runs, perform root cause analysis, and identify the real issues behind Power Automate failures.
image: /assets/posts/power-automate-runs/report.png
---

When Power Automate flows start failing, the traditional approach involves manually navigating through the Power Automate portal, clicking through run histories, and examining individual actions to find the root cause. With my Power Platform MCP server, I can streamline this entire investigation process using natural language prompts that automatically gather context, analyze failures, and provide actionable insights.

## The Scenario

I received reports that my managed identity HTTP flow was erroring in the dev environment. The assumption was that it might be related to managed identity authentication issues - a common culprit when flows work in one environment but fail in another. Rather than manually clicking through the Power Automate portal, I decided to use my MCP server to investigate.

## The Investigation Process

Using a simple natural language prompt, I asked the MCP agent to investigate the flow runs and report what happened. Here's what unfolded:

![Initial investigation prompt](/assets/posts/power-automate-runs/prompt.png)

### Step 1: Environment Discovery and Flow Identification

The agent automatically:
- Queried Dataverse for the dev environment ID
- Located the specific flow by searching for "managed identity http" candidates
- Retrieved the flow context needed for subsequent API calls

This context gathering eliminated the manual step of hunting through environments and flow lists.

### Step 2: Flow Run Analysis

The agent used the Power Automate APIs to:
- Fetch recent flow runs with filters for the last week
- Identify the pattern of failures (multiple runs failed on 2025-09-09 with 502 BadGateway/NoResponse)
- Provide direct links to the failed runs in the Power Automate portal

![Flow run analysis and links](/assets/posts/power-automate-runs/links.png)

The agent discovered that several runs failed on 2025-09-09 with consistent 502 BadGateway error codes, but also noted successful runs just prior to the failures - indicating an intermittent issue rather than a systemic authentication problem.

### Step 3: Deep Dive into Failed Runs

Rather than stopping at the surface-level error codes, the agent drilled into specific failed runs to examine the individual actions:

![Investigation and root cause analysis](/assets/posts/power-automate-runs/investigate.png)

### Step 4: Root Cause Discovery

The investigation revealed the true culprit:

![Root cause analysis showing divide by zero error](/assets/posts/power-automate-runs/report.png)

**The Real Issue**: A divide-by-zero error in the flow logic (`@{div(1,0)}`) that someone had added for testing purposes.

**What it Was NOT**: 
- ✗ Managed identity authentication issues
- ✗ HTTP connection problems  
- ✗ Environment configuration issues

**What it Actually Was**:
- ✅ **Divide by zero errors** in the flow logic (`@{div(1,0)}`)

The error message clearly showed: *"Attempt to divide an integral or decimal value by zero"* with the problematic expression highlighted in the `Do_stuff` action.

## Technical Capabilities Demonstrated

This investigation showcased several key capabilities of the MCP server's Power Automate integration:

### Flow Run Management
- **`get_flow_runs`**: List runs with optional filters for date ranges and status
- **`get_flow_run_actions`**: Retrieve detailed action results for failed runs
- **`get_flow_triggers`**: Examine trigger configurations and histories

## The Power of Automated Investigation

What typically would have taken multiple manual steps:

1. **Manual approach**: Navigate to Power Automate → Find environment → Locate flow → Check run history → Click failed runs → Examine each action → Identify the problematic expression
2. **MCP approach**: Single natural language prompt → Comprehensive analysis and root cause identification


## Related Posts

- [Power Platform MCP - Auditing HTTP / Teams Webhook Trigger URL Changes]({{ '/azure/2025/09/10/power-platform-mcp-http-trigger-url-audit.html' | relative_url }})
- [Power Platform MCP - Open source Dataverse MCP server]({{ '/azure/2025/09/09/power-platform-mcp-open-source.html' | relative_url }})

Source code: <https://github.com/Cliveo/Power-Platform-MCP>

The next time you have mysterious Power Automate failures, consider using an AI agent with API access to do the detective work. It's faster, more thorough, and helps you avoid getting caught up in the wrong troubleshooting rabbit holes.