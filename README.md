[![release](https://github.com/kra-ts/humio-fdr-utils/actions/workflows/release.yml/badge.svg)](https://github.com/kra-ts/humio-fdr-utils/actions/workflows/release.yml)
[![build](https://github.com/kra-ts/humio-fdr-utils/actions/workflows/build.yml/badge.svg)](https://github.com/kra-ts/humio-fdr-utils/actions/workflows/build.yml)


# Humio FDR Utilities

This [Humio Package](https://docs.humio.com/docs/packages/)  gives content to Humio that can aid and assist in formatting and parsing of data coming from CrowdStrike's Falcon platform with FDR.

The utiities are based on the data ingested are coming from the FDR parser bundled with the Humio package `crowdstrike/fdr`.

For ingesting the data this project assumes the use of [fdr2humio](https://github.com/humio/fdr2humio) project.

Documentation for understanding FDR and the events given, please refer to the documentation within the Falcon Platform.

| Region   |                                  Falcon Data Replicator                                  |                                   Event Data Dictonary                                    |
|----------|:----------------------------------------------------------------------------------------:|:-----------------------------------------------------------------------------------------:|
| US-1     |      [link](https://falcon.crowdstrike.com/documentation/9/falcon-data-replicator)       |      [link](https://falcon.crowdstrike.com/documentation/26/events-data-dictionary)       |
| US-2     |    [link](https://falcon.us-2.crowdstrike.com/documentation/9/falcon-data-replicator)    |    [link](https://falcon.us-2.crowdstrike.com/documentation/26/events-data-dictionary)    |
| EU-1     |    [link](https://falcon.eu-1.crowdstrike.com/documentation/9/falcon-data-replicator)    |    [link](https://falcon.eu-1.crowdstrike.com/documentation/26/events-data-dictionary)    |
| GOV-US-1 | [link](https://falcon.laggar.gcw.crowdstrike.com/documentation/9/falcon-data-replicator) | [link](https://falcon.laggar.gcw.crowdstrike.com/documentation/26/events-data-dictionary) |

# Table of contents

* [Installation](#installation)
  * [Upgrade](#upgrade)
* [Usage](#usage)
  * [<em>FalconURL(region)</em>](#falconurlregion)
  * [<em>RTR()</em>](#rtr)
  * [<em>ProcessExplorerTarget()</em>](#processexplorertarget)
  * [<em>ProcessExplorerParent()</em>](#processexplorerparent)
  * [<em>FileName()</em>](#filename)
  * [<em>FileNameMV()</em>](#filenamemv)
* [Contribution](#contribution)
* [License](#license)

# Installation

1. Go to the [latest release](https://github.com/kra-ts/humio-fdr-utils/releases/latest) and download the package.

2. In your Humio Cluster go to the repo/view you'd like to install the package.

3. Go to _Settings -> Packages -> Installed_ and click `import package` 

4. Drag and drop the zip file or select from filsystem and click install in the summary view

## Upgrade

Repeat the steps from [Installation](#installation) and click update

<br /><br />

# Usage

As this is a bundled package, when installed in Humio, the package will be reffered to as `crowdstrike/fdr-utils`. This means that to refer any of theese user functions, you have to prepend them with `$crowdstrike/fdr-utils:`followed up the function. Please refer to the _example_ section within each function.

## _FalconURL(region)_

Retrieve the correct base URL for the Falcon platform based on region  e.g. `https://falcon.eu-1.crowdstrike.com`.

<details><summary>Details</summary><br />

**Parameters**:

| name     | type   | required | default | Description                                     |
| -------- | :----: | :------: | :-----: | ----------------------------------------------- |
| `region` | string | No       | `US-1`  | Regions defined `US-1` `US-2` `EU-1` `US-GOV-1` |  

<br />

**Outputs**:

| name            | return type | type   | Description                                                          |
|-----------------|-------------|--------|----------------------------------------------------------------------|
| `falcon.region` | field       | string | The region parameter in all-uppercase                                |
| `falcon.url`    | field       | string | Will return the url for the Falcon platform for the specified region |

<br />

**Example**:

```
$crowdstrike/fdr-utils:FalconURL(region=eu-1)
```

</details><br />


## _RTR()_

With input of base URL for Falcon, generates a markdown RTR link to connect to the host.

<details><summary>Details</summary><br />

**Inputs**:

| name         | input type | type   | Description                     |
|--------------|------------|--------|---------------------------------|
| `aid`        | field      | string | AID of the target agent         |
| `#cid`       | field      | string | CID of the target tenant        |
| `falcon.url` | field      | string | Base URL of the Falcon platform |

<br />

**Outputs**:

| name         | return type | type   | Description                                          |
|--------------|-------------|--------|------------------------------------------------------|
| `falcon.RTR` | field       | string | Markdown fomatted string that gives a link to do RTR |

<br />

**Example**:

```
#type="FDR" #event_simpleName != * ComputerName = * aid = *
| groupBy(["#cid", "aid"], function=selectLast(["ComputerName"]))
| $crowdstrike/fdr-utils:FalconURL(region=eu-1)
| $crowdstrike/fdr-utils:RTR()
| table([aid, ComputerName, falcon.RTR])
```

</details><br />

## _ProcessExplorerTarget()_

With input of base URL for Falcon, generates a markdown link to show process explorer of that process.

<details><summary>Details</summary><br />

**Inputs**:

| name              | input type | type   | Description                        |
|-------------------|------------|--------|------------------------------------|
| `TargetProcessId` | field      | string | TargetProcessId to create link for |
| `aid`             | field      | string | AID of the target agent            |
| `#cid`            | field      | string | CID of the target tenant           |
| `falcon.url`      | field      | string | Base URL of the Falcon platform    |

<br />

**Outputs**:

| name                             | return type | type   | Description                                               |
|----------------------------------|-------------|--------|-----------------------------------------------------------|
| `falcon.process_explorer_target` | field       | string | Markdown fomatted string linking to the target process ID |

<br />

**Example**:

```
#type="FDR" #event_simpleName=ProcessRollup2 TargetProcessId=* /admin/i (net.exe or net1.exe)
| groupBy(["#cid", "aid", "TargetProcessId"], function=selectLast(CommandLine))
| $crowdstrike/fdr-utils:FalconURL(region=eu-1)
| $crowdstrike/fdr-utils:ProcessExplorerTarget()
| drop([#cid, falcon.region, falcon.url])
```

</details><br />

## _ProcessExplorerParent()_

With input of base URL for Falcon, generates a markdown link to show process explorer of that process.

<details><summary>Details</summary><br />

 **Inputs**:

| name              | input type | type   | Description                        |
|-------------------|------------|--------|------------------------------------|
| `ParentProcessId` | field      | string | ParentProcessId to create link for |
| `aid`             | field      | string | AID of the target agent            |
| `#cid`            | field      | string | CID of the target tenant           |
| `falcon.url`      | field      | string | Base URL of the Falcon platform    |

<br />

**Outputs**:

| name                             | return type | type   | Description                                               |
|----------------------------------|-------------|--------|-----------------------------------------------------------|
| `falcon.process_explorer_parent` | field       | string | Markdown fomatted string linking to the parent process ID |

<br />

**Example**:

```
#type="FDR" #event_simpleName=ProcessRollup2 ParentProcessId=* /admin/i (net.exe or net1.exe)
| groupBy(["#cid", "aid", "ParentProcessId"], function=[collect(CommandLine, separator="\r"), count()])
| $crowdstrike/fdr-utils:FalconURL(region=eu-1)
| $crowdstrike/fdr-utils:ProcessExplorerParent()
| drop([#cid, falcon.region, falcon.url])
```

</details><br />

## _FileName()_

Parses ImageFileName and returns as FileName

<details><summary>Details</summary><br />

 **Inputs**:

| name            | input type | type   | Description                        |
|-----------------|------------|--------|------------------------------------|
| `ImageFileName` | field      | string | ImageFileName contains the path to exract FileName from, e.g.<br /> `\Device\HarddiskVolume4\Windows\System32\net1.exe` |
<br />

**Outputs**:

| name       | return type | type   | Description                                               |
|------------|-------------|--------|-----------------------------------------------------------|
| `FileName` | field       | string | FileName for the inputted path, e.g. `net1.exe` |

<br />

**Example**:

```
#type = FDR "#event_simpleName" = ProcessRollup2
| groupBy(["aid", "ParentProcessId"], function={$FileName() | collect(FileName)})
```

</details><br />

## _FileNameMV()_

Parses ImageFileName (multivalue string) and returns as FileName as multivalue string

<details><summary>Details</summary><br />

 **Inputs**:

| name            | input type | type   | Description                                                                                                                                                                |
|-----------------|------------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ImageFileName` | field      | string | ImageFileName contains the paths to exract FileNames from, e.g.<br /> `\Device\HarddiskVolume4\Windows\System32\net1.exe \Device\HarddiskVolume4\Windows\System32\net.exe` |

<br />

**Outputs**:

| name       | return type | type   | Description                                              |
|------------|-------------|--------|----------------------------------------------------------|
| `FileName` | field       | string | FileNames for the inputted path, e.g. `net1.exe net.exe` |

<br />

**Example**:

```
#type = FDR "#event_simpleName" = ProcessRollup2
| groupBy(["aid", "ParentProcessId"], function=[count(), {collect(ImageFileName)}])
| $crowdstrike/fdr-utils:FileNameMV()
| drop(ImageFileName)
```

</details><br />

# Contribution

This is a sample repo started from posting on [a post](https://www.reddit.com/r/crowdstrike/comments/ry6ma0/20220107_cool_query_friday_adding_process/) from [Cool Query Friday](https://www.reddit.com/r/crowdstrike/collection/8016c539-c284-442c-9726-6bc05053d7a9/) at [r/crowdstrike](https://www.reddit.com/r/crowdstrike/).

Please feel free tro contribute at any time by doing a PR.

<br />

# License

[Apache License 2.0](/LICENSE)