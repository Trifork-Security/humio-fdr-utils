# Humio FDR Utilities

This [Humio Package](https://docs.humio.com/docs/packages/)  gives content to Humio that can aid and assist in formatting and parsing of data coming from CrowdStrike's Falcon platform with FDR.

The utiities are based on the data ingested are coming from the FDR parser bundled with the Humio package `crowdstrike/fdr`.

For ingesting the data this project assumes the use of [fdr2humio](https://github.com/humio/fdr2humio) project.

Documentation for understanding FDR and the events given, please refer to the documentation within the Falcon Platform.

| Region | Falcon Data Replicator | Event Data Dictonary |
| -------- | :--------------------------------------------------------------------------------------: | :----------------------------------------------------------------------------: |
| US-1     | [link](https://falcon.crowdstrike.com/documentation/9/falcon-data-replicator)            | [link](https://falcon.crowdstrike.com/documentation/26/events-data-dictionary) |
| US-2     | [link](https://falcon.us-2.crowdstrike.com/documentation/9/falcon-data-replicator)       | [link](https://falcon.us-2.crowdstrike.com/documentation/26/events-data-dictionary) |
| EU-1     | [link](https://falcon.eu-1.crowdstrike.com/documentation/9/falcon-data-replicator)       | [link](https://falcon.eu-1.crowdstrike.com/documentation/26/events-data-dictionary) |
| GOV-US-1 | [link](https://falcon.laggar.gcw.crowdstrike.com/documentation/9/falcon-data-replicator) | [link](https://falcon.laggar.gcw.crowdstrike.com/documentation/26/events-data-dictionary) |

# Table of contents

* [Installation](#installation)
* [Usage](#usage)
* [Contribution](#contribution)
* [License](#license)

# Installation

Will be described at later commit!

# Usage

As this is a bundled package, when installed in Humio, the package will be reffered to as `crowdstrike/fdr-utils`. This means that to refer any of theese user functions, you have to prepend them with `$crowdstrike/fdr-utils:`followed up the function. Please refer to the _example_ section within each function.

## _FalconURL(region)_

Retrieve the correct base URL for the Falcon platform based on region  e.g. `https://falcon.eu-1.crowdstrike.com`.

Parameters:

| name     | type   | required | default | Description                                     |
| -------- | :----: | :------: | :-----: | ----------------------------------------------- |
| `region` | string | No       | `US-1`  | Regions defined `US-1` `US-2` `EU-1` `US-GOV-1` |  

Outputs:

| name            | return type | type   | Description                                                             |
| ------------    | ----------- | ------ | ----------------------------------------------------------------------- |
| `falcon.region` | field       | string | The region parameter in all-uppercase                                   |
| `falcon.url`    | field       | string | Will return the url for the Falcon platform for the specified region    |

Example:

```
$crowdstrike/fdr-utils:FalconURL(region=eu-1)
```

## _RTR()_

With input of base URL for Falcon, generates a markdown RTR link to connect to the host.

Inputs:

| name         | input type  | type   | Description                     |
| ------------ | ----------- | ------ | ------------------------------- |
| `aid`        | field       | string | AID of the target agent         |
| `#cid`       | field       | string | CID of the target tenant        |
| `falcon.url` | field       | string | Base URL of the Falcon platform |

Outputs:

| name         | return type | type    | Description                                          |
| ------------ | ----------- | ------- | ---------------------------------------------------- |
| `falcon.RTR` | field       | string  | Markdown fomatted string that gives a link to do RTR |

Example:

```
#type="FDR" #event_simpleName != * ComputerName = * aid = *
| groupBy(["#cid", "aid"], function=selectLast(["ComputerName"]))
| $crowdstrike/fdr-utils:FalconURL(region=eu-1)
| $crowdstrike/fdr-utils:RTR()
| table([aid, ComputerName, falcon.RTR])
```

## _ProcessExplorerTarget()_

With input of base URL for Falcon, generates a markdown link to show process explorer of that process.

Inputs:

| name              | input type  | type   | Description                        |
| ----------------- | ----------- | ------ | ---------------------------------- |
| `TargetProcessId` | field       | string | TargetProcessId to create link for |
| `aid`             | field       | string | AID of the target agent            |
| `#cid`            | field       | string | CID of the target tenant           |
| `falcon.url`      | field       | string | Base URL of the Falcon platform    |

Outputs:

| name                             | return type | type    | Description                                               |
| -------------------------------- | ----------- | ------- | --------------------------------------------------------- |
| `falcon.process_explorer_target` | field       | string  | Markdown fomatted string linking to the target process ID |

Example:

```
#type="FDR" #event_simpleName=ProcessRollup2 TargetProcessId=* /admin/i (net.exe or net1.exe)
| groupBy(["#cid", "aid", "TargetProcessId"], function=selectLast(CommandLine))
| $crowdstrike/fdr-utils:FalconURL(region=eu-1)
| $crowdstrike/fdr-utils:ProcessExplorerTarget()
| drop([#cid, falcon.region, falcon.url])
```

## _ProcessExplorerParent()_

With input of base URL for Falcon, generates a markdown link to show process explorer of that process.

Inputs:

| name              | input type  | type   | Description                        |
| ----------------- | ----------- | ------ | ---------------------------------- |
| `ParentProcessId` | field       | string | ParentProcessId to create link for |
| `aid`             | field       | string | AID of the target agent            |
| `#cid`            | field       | string | CID of the target tenant           |
| `falcon.url`      | field       | string | Base URL of the Falcon platform    |

Outputs:

| name                             | return type | type    | Description                                               |
| -------------------------------- | ----------- | ------- | --------------------------------------------------------- |
| `falcon.process_explorer_parent` | field       | string  | Markdown fomatted string linking to the parent process ID |

Example:

```
#type="FDR" #event_simpleName=ProcessRollup2 ParentProcessId=* /admin/i (net.exe or net1.exe)
| groupBy(["#cid", "aid", "ParentProcessId"], function=[collect(CommandLine, separator="\r"), count()])
| $crowdstrike/fdr-utils:FalconURL(region=eu-1)
| $crowdstrike/fdr-utils:ProcessExplorerParent()
| drop([#cid, falcon.region, falcon.url])
```

# Contribution

This is a sample repo started from posting on [a post](https://www.reddit.com/r/crowdstrike/comments/ry6ma0/20220107_cool_query_friday_adding_process/) from [Cool Query Friday](https://www.reddit.com/r/crowdstrike/collection/8016c539-c284-442c-9726-6bc05053d7a9/) at [r/crowdstrike](https://www.reddit.com/r/crowdstrike/).

Please feel free tro contribute at any time by doing a PR.

# License

[Apache License 2.0](https://github.com/kra-ts/humio-fdr-utils/blob/main/LICENSE)
