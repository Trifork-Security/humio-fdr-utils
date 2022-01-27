# Humio FDR Utilities

This repo is a Humio Package that gives content to Humio that can aid and assist in formatting and parsing of data coming from CrowdStrike FDR.

## Getting Falcon URL and creating RTR & process explorer links.

By getting the Falcon URL for your region by calling `$crowdstrike/fdr-utils:FalconURL(region="EU")` which then can be leveraged by the functions `$crowdstrike/fdr-utils:RTR()`, `$crowdstrike/fdr-utils:ProcessExplorerTarget()` and `$crowdstrike/fdr-utils:ProcessExplorerParent()`

```
$crowdstrike/fdr-utils:FalconURL(region="EU")
| $crowdstrike/fdr-utils:RTR()
| $crowdstrike/fdr-utils:ProcessExplorerTarget()
| table([RTR, "Process Explorer"]) 
```

# Support
This is inital repo for tools to come when I need them. Feel free to enrich this at any time!
