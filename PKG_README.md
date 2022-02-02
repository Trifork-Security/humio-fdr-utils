# Humio FDR Utilities

This [Humio Package](https://docs.humio.com/docs/packages/)  gives content to Humio that can aid and assist in formatting and parsing of data coming from CrowdStrike's Falcon platform with FDR.

The utiities are based on the data ingested are coming from the FDR parser bundled with the Humio package `crowdstrike/fdr`.

For ingesting the data, this project assumes the use of [fdr2humio](https://github.com/humio/fdr2humio) project.

Documentation for understanding FDR and the events given, please refer to the documentation within the Falcon Platform.

# Usage

As this is a bundled package, when installed in Humio, the package will be reffered to as `crowdstrike/fdr-utils`. This means that to refer any of theese user functions, you have to prepend them with `$crowdstrike/fdr-utils:`followed up the function. Please refer to the [Github Project](https://github.com/Trifork-Security/humio-fdr-utils) for additional documentation. 

# Contribution

This is a sample repo started from posting on [a post](https://www.reddit.com/r/crowdstrike/comments/ry6ma0/20220107_cool_query_friday_adding_process/) from [Cool Query Friday](https://www.reddit.com/r/crowdstrike/collection/8016c539-c284-442c-9726-6bc05053d7a9/) at [r/crowdstrike](https://www.reddit.com/r/crowdstrike/).

Please feel free tro contribute at any time by doing a PR.

# License

[Apache License 2.0](https://github.com/Trifork-Security/humio-fdr-utils/blob/main/LICENSE)
