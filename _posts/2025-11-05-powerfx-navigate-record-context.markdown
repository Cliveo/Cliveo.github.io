---
layout: post
title:  "Power Fx Navigate with Record Context - The Hidden Object Wrapper"
date:   2025-11-04 18:00:00 +1300
categories: Azure
description: A quick fix for the frustrating "Unsupported property '_ownerid_value'" error when navigating between custom pages with record context in Power Fx.
---

Recently I struggled to navigate between custom pages in Power Fx while passing a record as context. The error was vague and the solution wasn't obvious.

## The Problem

I tried the straightforward approach:

```powerfx
Navigate('App Name', record);
```

This threw a generic error: "Unsupported property '_ownerid_value'." The error message didn't point to the real issue, making it difficult to diagnose.

## The Solution

The trick is to wrap the record in an object:

```powerfx
Navigate('App Name', { Record: record });
```

That's it. By wrapping the record in an object with a named property, Power Fx can properly pass the context to the target page.

## Why This Works

When you pass a bare record, Power Fx tries to destructure its properties directly, including internal properties like `_ownerid_value` that aren't meant to be exposed. By wrapping it in an object, you're telling Power Fx to treat it as a single, named context variable that the target page can reference.

## Takeaway

Small syntax changes like this can be annoyingly hard to spot. If you're getting cryptic property errors when navigating with records, try wrapping your context in an object first.

For more details on the Navigate function and its parameters, see the [official Power Fx Navigate documentation](https://learn.microsoft.com/en-us/power-platform/power-fx/reference/function-navigate#overview){:target="_blank"}.
