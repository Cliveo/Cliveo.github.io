---
layout: post
title:  "Dataverse MCP: Discovering and Documenting Your Tables"
date:   2025-07-01 14:30:00 +1300
categories: Azure
---

Following up on my previous post about [setting up Dataverse MCP](/azure/2025/06/17/dataverse-mcp-setup-experience.html), I wanted to share a practical example of how powerful this integration can be for exploring and documenting your Dataverse environment.

# The Challenge: Understanding Your Environment

One common challenge developers face is getting a comprehensive overview of their Dataverse environment. 

Sure, you can navigate through the Power Platform admin center or use the maker portal and thanks to the community there are a ton of amazing tools available in XrmToolbox, but what if you want to quickly generate documentation or get an AI assistant to understand your entire data model?

This is where Dataverse MCP really shines.

# Two Key MCP Tools for Discovery

## List Tables Tool

The `list_tables` tool gives you a complete inventory of your environment:

```json
{
  "tables": "aaduser,account,aciviewmapper,actioncard,..."
}
```

![list_tables](\assets\mcp-discovery\list-tables.png)


In my environment, this returned **659 tables** - everything from core business entities like `account` and `contact` to system tables, AI components, and Power Platform artifacts.

## Describe Table Tool

The `describe_table` tool provides detailed schema information for any specific table:

```sql
-- Collection Name: accounts
CREATE TABLE account (
  accountid NVARCHAR(MAX),
  accountnumber VARCHAR(20),
  name VARCHAR(160) NOT NULL,
  telephone1 VARCHAR(50),
  emailaddress1 VARCHAR(100),
  websiteurl VARCHAR(200),
  address1_city VARCHAR(80),
  address1_country VARCHAR(80),
  revenue NVARCHAR(MAX),
  numberofemployees INT,
  industrycode NVARCHAR(MAX),
  -- ... and many more fields
);
```

![describe_tables](\assets\mcp-discovery\describe-table.png)

This is incredibly valuable for understanding field types, constraints, and relationships.

I found it quite strange the output looking like a SQL create query, but this seemed to be very consumable for Claude. 

It was able to then use this information to write quite accurate documentation & use the metadata it extracted to start writing very practical scripts.

# Practical Application: Auto-Generated Documentation

Using these two tools together, I was able to generate comprehensive documentation that includes:

- **Complete table inventory** (659 tables categorized by function)
- **Detailed schemas** for key business tables
- **Field descriptions** and data types
- **Categorized organization** (Core Business, AI & Automation, Security, etc.)

The entire process took minutes rather than hours of manual exploration.

# Key Benefits for Developers

## 1. **Rapid Environment Assessment**
Quickly understand what's available in any Dataverse environment without clicking through countless screens.

## 2. **AI-Enhanced Development**
Your AI assistant now has complete context about your data model, making code generation and queries much more accurate.


# Interesting abilities

I found I was able to query the organization table, allowing me to see what organization settings had been enabled.

## Querying Organization Settings

For example, I could check if auditing was enabled:

![Querying organization audit settings](\assets\mcp-discovery\audit-enabled.png)

## Updating Organization Settings

Even more impressive - I was able to update organization-level settings directly through the MCP interface:

![Updating organization audit settings](\assets\mcp-discovery\audit-disabled.png)

This demonstrates that MCP provides both **read and write capabilities**, not just discovery. You can:

- Query system configuration
- Update organization settings

# Current Limitations

## System Metadata Access

The current implementation doesn't provide direct access to all tables or things accessible through WebAPI. I found I couldn't get access to some things like `EntityMetadata`. So I struggled to query entity-level settings such as `Auditing`

## Model Response Length

A few times with these bigger queries I'd get:

`Sorry, the response hit the length limit. Please rephrase your prompt`

To get around this you can often break down the problem, so instead of trying to document all of the tables at once. You ask the agent to iterate 10 tables at a time, so that it can document within its bounds.

# Pro Tip: Combine with GitHub Copilot

When writing Power Platform code, having your AI assistant understand your exact table schema is game-changing. Instead of guessing field names or looking them up manually, the AI can suggest accurate field references and proper data types.

This by no means replaces incredibly valuable tools like early bound generation. 

However in use cases like PowerShell scripting it can be incredibly useful to guarantee accuracy.

---

*Have you tried Dataverse MCP yet? What's your experience been like? Share your thoughts in the comments or connect with me on [LinkedIn](https://www.linkedin.com/in/cliveoldridge/).*
