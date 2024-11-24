---
layout: post
title:  "Dataverse Git Integration Setup"
date:   2024-11-11 14:48:01 +1300
categories: Azure
---

I’m going to share a simple guide to setup Dataverse Git integration. Hopefully this helps a broader audience the opportunity to get involved with the preview & get familiar with the functionality that’s coming.

I believe this functionality will some day soon be part of standard practice and used daily by makers using power platform.

The documentation can be found [here](https://learn.microsoft.com/en-us/power-platform/alm/git-integration/connecting-to-git){:target="_blank"}

Microsoft’s blog announcing it can be found [here](https://www.microsoft.com/en-us/power-platform/blog/power-apps/introducing-git-integration-in-power-platform-preview/){:target="_blank"}

# Step 1: Azure Devops
You will need a project with git set up in Azure Devops, it will need to be in the same tenant as your Dataverse environment.

Navigate to [devops](https://dev.azure.com/){:target="_blank"}

Select new `project`

Enter your preferred details:

![here](/assets/dataverse-git-integration/1.png)

Once the project has been created

Navigate to repos

Select initialize (you need a repo & branch created for the next step)

![here](/assets/dataverse-git-integration/2.png)

At this point you could choose to create some branches, depending on what you’re doing. I’m going to create a branch per developer environment for testing this functionality.

![here](/assets/dataverse-git-integration/3.png)

# Step 2: Create a Dataverse environment
At the time of writing this you need an environment in early release & a select few regions. I’m going to use Australia, a managed environment & early release features enabled.

![here](/assets/dataverse-git-integration/4.png)

# Step 3: Configure Dataverse Git integration
Navigate to the [preview maker portal](https://make.preview.powerapps.com/){:target="_blank"}

(Eventually you will no longer need to use the preview maker portal)

Choose the environment you’ve created

Navigate to `solutions`

Select `Connect to Git`

![here](/assets/dataverse-git-integration/5.png)

Now in this example I’m going to use the environment connection type, I’m leaning towards this as my favorite structure, I’ll save the justification for a future post, but basically I think it’s going to be the best option If I intend to use this integration to merge customization between developer environments.

![here](/assets/dataverse-git-integration/6.png)

You should see a success notification.

![here](/assets/dataverse-git-integration/7.png)

# Step 4: Sync a solution
Create a solution you’d like to synchronize

![here](/assets/dataverse-git-integration/8.png)

Add some components to your solution.

I’ll start with adding an out of the box table e.g. Account

As this is an existing table, you should only include the things you intend to customize. Including all objects creates noise & will slow things down.

![here](/assets/dataverse-git-integration/9.png)

When you first navigate to source control – you will usually see a message that its processing the components that have been added.

![here](/assets/dataverse-git-integration/10.png)

After a few seconds it should be ready to refresh

Select commit

Add a relevant commit message

![here](/assets/dataverse-git-integration/11.png)

You should get a successful commit notification with a link to the commit.

![here](/assets/dataverse-git-integration/12.png)

If you click on the link It should open the commit in devops

![here](/assets/dataverse-git-integration/13.png)

# Conclusion:
And that’s it! You now have a developer environment, configured with Dataverse Git integration, committed to source control & hosted in Azure devops.

You might notice some changes in the files exported. It looks a bit different to the typical solution unpack. The structure has changed, file names have changed and the contents is `yml` rather than `xml`.

The `yml` is much lighter, easier to read, smaller packages to commit and a big improvement on the previous solution packages.