---
layout: post
title: "Power Platform MCP - Open source Dataverse MCP server"
date: 2025-09-10 10:00:00 +1200
categories: Azure
---

I have published the source code that powered my recent Wellington Power Platform User Group session. The repo contains a lightweight MCP server for the Dataverse Web API that I use daily for discovery, documentation, and quick admin tasks.

Repo: <https://github.com/Cliveo/Power-Platform-MCP>

## What it is

An MCP server for Power Platform that exposes practical tools for Dataverse and Power Automate so agents can work with your environment context. It focuses on fast reads and a small number of targeted admin actions.

## Features

Dataverse
- Query plugin trace logs with OData filters (plugintracelogs)
- Generic OData GET with formatted values
- Activate or deactivate classic workflows
- List entities (EntityDefinitions)
- Get detailed metadata for an entity including attributes and relationships

Power Automate
- List triggers for a flow
- Get the manual trigger callback URL
- List runs with optional filters like since and status
- Get run details and actions
- Get trigger histories

## Watch the talk

Watch it on the blog: [Agentify your workflow with MCP - Wellington Power Platform UG](/azure/2025/08/29/agentify-your-workflow-with-mcp-wppug.html)


## Get started

- Prereqs: .NET 10 SDK, Azure CLI logged in with `az login`, and access to your Dataverse environment.
- Configure your environment values in `.github/copilot-instructions.md` under "Your environment (quick-config)". Set org URL, publisher prefix, flow API base URL, and environmentId.
- The server is registered in `.vscode/mcp.json`. Start it from GitHub Copilot Agent mode, or build manually if you prefer.
- Auth: uses DefaultAzureCredential, which will consume the Azure CLI token locally after `az login`.

Clone the repo, review the README, and follow the setup steps above. The project is intended to be simple to run locally so you can iterate on custom tools that fit your workflow.

## Related posts

- [Dataverse MCP - Discovering and Documenting Your Tables](/azure/2025/07/01/dataverse-mcp-table-discovery.html)
- [Agentify your workflow with MCP - Wellington Power Platform UG](/azure/2025/08/29/agentify-your-workflow-with-mcp-wppug.html)

Feedback and issues are welcome in the GitHub repo. If you try it, let me know what tools you would like to see next.
