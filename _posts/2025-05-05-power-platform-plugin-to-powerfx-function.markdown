---
layout: post
title:  "Power Platform Plugin to Power Fx Function – Synchronous PowerFx from Plugins"
date:   2025-05-05 10:00:00 +1300
categories: Azure
---

Recently, I discovered that Microsoft has de-prioritized automated low-code plug-ins in Dataverse, which prompted me to explore how to invoke Power Fx functions from Dataverse plugins. This post shares my findings and a practical approach to synchronously call Power Fx logic from plugin events like create or update.

> **Note:** For a comprehensive overview of Power Fx functions in Dataverse, see the [official documentation](https://learn.microsoft.com/en-us/power-apps/maker/data-platform/functions-overview).

# Motivation

The official documentation ([Invoke Power Fx functions](https://learn.microsoft.com/en-us/power-apps/maker/data-platform/functions-invoke)) covers invoking Power Fx functions from:
- Canvas apps or custom pages
- Power Automate cloud flows
- The Dataverse Web API
- Other Power Fx functions

Seeing that it's available by Web API, I decided to try see if I could find a useful way to link plugins to Power FX.

# Why is this important?

- **Transactional logic:** Power Fx can now participate in the same transaction as the plugin event, enabling rollup calculations, advanced validation, or data enrichment.
- **Extensibility:** You can use Power Fx for scenarios not possible with classic tools, and keep your logic maintainable and low-code.
- **Generic approach:** By making the plugin generic (passing the function name as an unsecure configuration), you can reuse it for any function.

# How does it work?

Power Fx functions are exposed as custom actions in Dataverse. This means you can invoke them using a standard `OrganizationRequest` with the schema name of the function. In my plugin, I pass the ID of the target record (from the plugin execution context) to the function, so the Power Fx logic can retrieve and operate on the triggering record.

This approach allows you to execute Power Fx synchronously, within the same transaction as the plugin event.

# Example Plugin Code

Here's a simplified version of my generic plugin:

```csharp
// ...existing using statements...

namespace ManagedIdentityPlugin
{
    /// <summary>
    /// Calls a custom API (PowerFx function) with the Target parameter set to the target entity's GUID. The API name is provided via unsecure configuration.
    /// </summary>
    public class CallPowerFxFunction : PluginBase
    {
        private readonly string _customApiName;

        public CallPowerFxFunction(string unsecureConfiguration, string secureConfiguration) : base(typeof(CallPowerFxFunction))
        {
            if (string.IsNullOrWhiteSpace(unsecureConfiguration))
                throw new ArgumentException("Custom API name must be provided in unsecure configuration.");
            _customApiName = unsecureConfiguration;
        }

        protected override void ExecuteDataversePlugin(ILocalPluginContext localPluginContext)
        {
            // ...existing null checks...

            var context = localPluginContext.PluginExecutionContext;
            var service = localPluginContext.PluginUserService;

            if (context.InputParameters.Contains("Target") && context.InputParameters["Target"] is Entity target)
            {
                var request = new OrganizationRequest(_customApiName);
                request["Target"] = target.Id.ToString();
                service.Execute(request);
            }
            else
            {
                throw new InvalidPluginExecutionException("Target entity reference not found in InputParameters.");
            }
        }
    }
}
```

- The function name is passed as the unsecure configuration.
- The plugin passes the target record's ID to the function.
- The function can then retrieve the record and execute any Power Fx logic.

# Plugin Registration Example

Below is how I registered the plugin step. The unsecure configuration contains the name of the Power Fx function (`cr17a_Replaceaccountname`). The step is registered on the `Update` message for the `email` entity, in the `PostOperation` stage, and set to run synchronously.

![Plugin Registration Step](/assets/pfx-function/prt.png)

# Plugin Trace Viewer Output

Here is a snippet from the Plugin Trace Viewer showing the plugin executing and calling the Power Fx function via the custom API. You can see the function name and execution times for each invocation.

![Plugin Trace Viewer](/assets/pfx-function/executed.png)

# Use Cases

- **Rollup calculations:** Aggregate data from related records and update fields in real time.
- **Complex validation:** Enforce business rules that are difficult to express in classic workflows.
- **Data enrichment:** Fetch and compute values from across the Dataverse.

# Conclusion

While Microsoft has de-prioritized automated Power Fx functions, you can still leverage their power synchronously by invoking them from plugins. This approach bridges the gap between low-code and pro-code, enabling maintainable, transactional business logic for advanced scenarios.

If you have questions or want to share your own use cases, feel free to reach out!
