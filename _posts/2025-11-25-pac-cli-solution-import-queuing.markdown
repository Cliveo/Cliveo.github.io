---
layout: post
title:  "PAC CLI Now Queues Solution Imports Instead of Failing"
date:   2025-11-24 18:00:00 +1300
categories: Azure
description: A quiet but welcome improvement to the Power Platform CLI that queues solution imports instead of throwing errors when another import is in progress.
image: /assets/posts/pac-cli-solution-queuing/waiting-for-event.png
---

I recently noticed a quiet improvement to the Power Platform CLI that makes a real difference to deployment workflows.

## The Old Behaviour

Previously, when you tried to import a solution while another import was already in progress, you'd get a generic error telling you to check solution history. This was frustrating during batch deployments or when multiple team members were deploying simultaneously.

## The New Behaviour

Now the CLI queues up the system jobs with a "Waiting for event" status and processes each solution sequentially as each one completes. No more failed imports, no more manual retries.

![Solution imports queued with Waiting for event status](/assets/posts/pac-cli-solution-queuing/waiting-for-event.png)

## Why This Matters

These small, unannounced improvements add up. Fewer failed deployments means less time troubleshooting and more time building. If you're running CI/CD pipelines or batch importing solutions, this change removes a common point of failure.

For more on the PAC CLI, see the [official documentation](https://learn.microsoft.com/en-us/power-platform/developer/cli/reference/solution#pac-solution-import){:target="_blank"}.
