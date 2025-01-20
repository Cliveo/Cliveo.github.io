---
layout: post
title:  "Power Platform - Solution Review - Solution.xml"
date:   2025-01-19 14:48:01 +1300
categories: Azure
---

In the series I'm going to try and document the focus areas for the robust review of solution components when deploying Power Platform - Dataverse solutions.

# Motivation

I've always done reviews of Dataverse Solutions & their related pull requests, but I've never taken the opportunity to write down what I'm looking for and why. 

I'm hoping the process of trying to articulate what I see in the solution files & why it might be important will help put some structure around the intent of reviewing.

The ultimate goal is to know what to look for to improve the quality of the customization I'm supporting the development of. 

This is intended to be a guide for myself but I'd love any contribution from the community to help me add to it.

# Solution.xml

![here](/assets/solution-review/solution/1.png)


The `solution.xml` file is a very important file to review as part of robust ALM when working within the Power Platform.

This file will contain details about:
- `UniqueName` - Used to uniquely identify the solution
- `LocalizedName` - The friendly name of the solution
- `Descriptions` - Useful to populate so that users of the solution can quickly get additional context
- `Version` - Useful as an identifier for 
    - Solution contents (e.g. version 1.x contains ... but 1.1 contains ...)
    - Tracking defects
    - Validating (the state of environments and their customization) & Planning releases
- `Publisher` - Used to identify the provider of the solution
- `RootComponents` - The top level components contained within the solution (e.g. tables)
- `MissingDependencies` - The required components that might be missing from your solution (`arguably the most important thing to review`)

# Names and descriptions

The `name` of the `solution` should be:
- `Appropriate` (is this acceptable to the customer? is it meaningful? is it correct?)
- `Descriptive` (should accurately describe the functional domain)
- `Consistent` (follow any existing conventions around naming)

The description of the solution should:
- `Actually be populated` (how often is it blank xD)
- `Summarize` 
    - Describe the solutions purpose
    - Provide important information about its contents
    - Point to useful resources

# Version

The `version` is an interesting attribute of the solution. Ideally the solutions version will allow you to:
- `Track Solution contents` (version 1.x contains ... but 1.1 contains ...)
- `Tracking defects` (defect found in 1.0 fix applied in 1.1)
- `Validate` the state of environments and their customization (`TEST` has 1.0 but `UAT` has 1.1)
- `Plan releases` (We will deploy 1.1 on this date and it contains ...)


I've seen a range of projects where the version is largly `meaningless` but I believe this is `wrong`.

Every implementation might be using solution versions in different ways so I'll keep this section brief. A few different versioning approaches could be:
- when the solution is `unpacked` (I'll focus on this option as it's the one I'm using at the moment)
- when the release `artifact` is generated
- when the `build & deploy` is happening

When reviewing the version in a pull request it can give you some information:
- Was this just a regular unpack? (revision changing)
- Is this a [clone of solution](https://learn.microsoft.com/en-us/dynamics365/customerengagement/on-premises/customize/use-segmented-solutions-patches-simplify-updates?view=op-9-1#clone-a-solution){:target="_blank"}? (minor changing)

I'd love to hear from others about the importance of versioning & how they use it in their implementations.

# Publisher

The main thing I'm checking for here is:
- Is it the `correct publisher`? On all of the projects I work on we use a `single publisher` for `all feature solutions within that project.`

If you'd like to read more about the importance of having the correct publisher. I encourage you to check out some posts by [Rob Wood](https://www.linkedin.com/in/rnwood/){:target="_blank"} & [Sean Astrakhan](https://www.linkedin.com/in/sean-astrakhan/){:target="_blank"}

# Root components

![here](/assets/solution-review/solution/2.png)

When looking at root components I'm looking for things like:
- Does this component `belong in this solution`
- What's being `added/removed`?
    - Is the `user story` associated to the `pull request` relevant to these `component changes`?
    

- Are the related files for the root component changes included? This is mostly an issue if you have some:
    - `Code first` development OR
    - `Chery picking` of solution components where there could be mistakes between the list of root components & the related customization files

# Missing dependencies

![here](/assets/solution-review/solution/3.png)

Now we are at the main event. `Missing dependencies` can be the most difficult part to review, but it's arguably the most important. 

Often there is a lot of noise with dependencies on out of the box components, this can make it tempting to avoid, but my recommendation is `pay attention`.

To help you decrypt this portion of the file it's useful checking the [solution component types](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/webapi/reference/solutioncomponent?view=dataverse-latest){:target="_blank"} available in the Microsoft documentation.

In my example you can see:
- I need a `table` to `exist` called `Book` in the `target environment` as my solution has customized that table but isn't providing the whole table.
- I've included a `form` called `Information` - but this form has `3 fields` I'm not providing so those `3 fields` must `already` exist in the `target environment`.

`Missing dependencies` will tell you what things `are` in your `solution` but have a `dependency` on things that are `not` in the `solution`. 

This means that to be able to `deploy this solution`, the `required missing components` must already exist in a `target environment` for this solution to be able to be `imported`. 

So you will either have to:
- Deploy that `other solution` OR 
- You should be working on the current solution to `remove dependencies`.


## Less dependencies less problems

Now `solution segmentation` & how to `manage layering` could be the contents of several large books.

**A general rule**: `Keep your dependencies to a minimum`, will make it easier to `release` & `maintain` the solution. 

Keeping an eye on `dependencies` will make sure you don't have any nasty surprises when trying to `release`. It also gives you an `opportunity` to make sure you haven't introduced an `invalid dependency` on another features components 

**Fixing dependencies can be an absolute nightmare - It's always easier to keep them clean up front than it is to retrospectively untangle**