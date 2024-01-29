# ReversingLabs rl-secure GitLab CI Configuration Examples

This repository contains a working example of GitLab workflow to illustrate scanning with the
[ReversingLabs secure.software Portal](https://docs.secure.software/portal/integrations/).

ReversingLabs secure.software Portal is capable of scanning
[nearly any type](https://docs.secure.software/concepts/language-coverage)
of software artifact or package that results from a build.

In this example, we use the source code and Maven build instructions for the Struts2 showcase web app,
which came with
[Apache Struts v2.5.28](https://archive.apache.org/dist/struts/2.5.28/).

This example relies on the
[ReversingLabs GitLab CI integration](https://github.com/reversinglabs/rl-scanner-gitlab-include)
include file and includes it as a remote configuration file.

The following example is provided in this directory:

- **.gitlab-ci.yml**

The `rl-scanner-cloud` example requires that you define the `RLPORTAL_ACCESS_TOKEN` secret environment variable to store your ReversingLabs
[Portal access token](https://docs.secure.software/api/generate-api-token).

You can define these secrets via your GitLab repository `Settings -> CI/CD -> Variables`.


## .gitlab-ci.yml

This pipeline script builds the WAR file and scans it using the
[ReversingLabs rl-scanner-cloud Docker image](https://hub.docker.com/r/reversinglabs/rl-scanner-cloud).

In order to be shared between jobs,
the build artifact (the WAR file)  needs to be uploaded directly to the job.

After the file is scanned,
analysis reports are saved as pipeline artifacts
to the `$REPORT_PATH` directory provided by user as a variable.

**Supported parameters**

| Name | Required | Description |
| ---- | -------- | ----------- |
| `RLPORTAL_ACCESS_TOKEN` | **Yes** | A Personal Access Token for authenticating requests to the secure.software Portal. Before you can use this example, you must [create the token](https://docs.secure.software/api/generate-api-token) in your Portal settings. Tokens can expire and be revoked, in which case you'll have to update this value. |
| `RLPORTAL_SERVER`       | **Yes** | The name of the secure.software Portal instance to use for the scan. The Portal instance name usually matches the subdirectory of `my.secure.software` in your Portal URL. For example, if your portal URL is `my.secure.software/demo`, the instance name to use with this parameter is `demo`. |
| `RLPORTAL_ORG`          | **Yes** | The name of a secure.software Portal organization to use for the scan. The organization must exist on the Portal instance specified with `RLPORTAL_SERVER`. The user account authenticated with the token must be a member of the specified organization and have the appropriate permissions to upload and scan a file. Organization names are case-sensitive. |
| `RLPORTAL_GROUP`        | **Yes** | The name of a secure.software Portal group to use for the scan. The group must exist in the Portal organization specified with `RLPORTAL_ORG`. Group names are case-sensitive. |
| `RL_PACKAGE_URL`        | **Yes** | The package URL (PURL) used to associate the file with a project and package on the Portal. PURLs are unique identifiers in the format `[pkg:type/]<project></package><@version>`. When scanning a file, you must assign a PURL to it, so that it can be placed into the specified project and package as a version. If the project and package you specified don't exist in the Portal, they will be automatically created.  |
| `ARTIFACT2SCAN`         | **Yes** | The artifact (file) you want to scan. The file must be in any of the [formats supported by secure.software](https://docs.secure.software/concepts/reference). |
| `PACKAGE_PATH`          | **Yes** | The location (relative to the working directory) where we find the ARTIFACT2SCAN. |
| `REPORT_PATH`           | No      | Path to the location where you want to store analysis reports. Must be relative to the `System.DefaultWorkingDirectory` |

## Pipeline result

The scan in this example will produce the `FAIL` CI status.
The pipeline will fail, as the scanner detects 4 critical issues: `Found 4 vulnerabilities matching selected criteria.`

## Useful resources

  1. [The official secure.software Portal documentation](https://docs.secure.software/portal/)
  2. The official `reversinglabs/rl-scanner-cloud` Docker image [on Docker Hub](https://hub.docker.com/r/reversinglabs/rl-scanner-cloud)
