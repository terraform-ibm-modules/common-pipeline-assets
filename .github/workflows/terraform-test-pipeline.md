# Terraform-Module-CI GitHub Action

The Terraform-Module-CI action is a GitHub Action designed to provide continuous integration for Terraform modules. It provides automated build, testing, and compliance scans for your Terraform projects.

## Overview

This action operates on workflow calls and is composed of two jobs:

- **CI Pipeline**: This job checks out your repository, sets ownership, installs dependencies, and runs pre-commit against all files.
- **Infrastructure Tests**: This job again checks out your repository, sets ownership, installs dependencies, runs a CRA scan (if a `craTarget` is specified), and runs unit tests.

## Usage

To use this action in your GitHub workflow, you will need to provide input parameters as per your requirements. The following inputs can be configured:

| Input Parameter           | Description                                                                                                                      | Required | Default Value                          |
|---------------------------|----------------------------------------------------------------------------------------------------------------------------------|----------|----------------------------------------|
| `checkout_commit`         | Commit ID you want to checkout.                                                                                                  | Yes      |                                        |
| `craConfigYamlFile`       | A YAML config file containing the configuration for CRA V2. If used, the other CRA inputs are overwritten.                       | No       |                                        |
| `craTarget`               | Target directory for CRA scan (comma-separated for multiple scans). If not provided, the CRA Scan will not be run.               | No       |                                        |
| `craGoalIgnoreFile`       | CRA Ignore file to use (for SCC v1). If not provided, it checks the repo root directory for `cra-tf-validate-ignore-goals.json`. | No       | "cra-tf-validate-ignore-goals.json"    |
| `craRuleIgnoreFile`       | CRA Ignore file to use (for SCC v2). If not provided, it checks the repo root directory for `cra-tf-validate-ignore-rules.json`. | No       | "cra-tf-validate-ignore-rules.json"    |
| `craSCCv2`                | Flag to use SCCv2 CRA `run-cra-scc-v2.sh`.                                                                                       | No       | false                                  |
| `instanceID`              | The ID of the SCC instance to get the profile from.                                                                              | No       | "1c7d5f78-9262-44c3-b779-b28fe4d88c37" |
| `sccRegion`               | The region in which the SCC instance is in.                                                                                      | No       | "us-south"                             |
| `profileID`               | The Profile ID input for CRA SCC v2. Ensure to use a US-specific ID.                                                             | No       | "262b5a6d-9dea-400e-b61f-0fbd63883f78" |
| `craEnvironmentVariables` | An optional list of environment variables for CRA in the format 'VAR1=value1,VAR2=value2'. Useful for providing TF_VARs.         | No       |                                        |
| `tfswitchDir` | Specify the location of a .tf file with the terraform constraint defined to automatically download the latest terraform version in the defined range.   | No       |  Defaults to the repos root level (aka ".")                                      |
| `GITHUB_BASE_REF`         | The target branch merging into.                                                                                                  | Yes      |                                        |
| `GITHUB_HEAD_REF`         | The source branch coming from.                                                                                                   | Yes      |                                        |

## Jobs

Each job executes on the latest Ubuntu runner, and uses a predefined container image `icr.io/goldeneye_images/goldeneye-ci-image:stable`.

### CI Pipeline Job

This job provides a basic CI pipeline, which includes steps like repository checkout, setting file ownership, dependency installation, and running pre-commit.

### Infrastructure Tests Job

This job primarily focuses on running infrastructure tests. In addition to checking out the repository, setting ownership, and installing dependencies, it runs a CRA scan based on provided configuration and unit tests.

## Secrets

The action uses several secrets that need to be configured at the ORG level:

| Secret Name              | Description                                |
|--------------------------|--------------------------------------------|
| `IBMCLOUD_API_KEY`       | IBM Cloud API Key.                         |
| `IAC_GE_OPS_TOOLCHAIN_ID`| A persistent toolchain ID for the account which the IBM Cloud API Key has access to. This is ignored if `TOOLCHAIN_ID` is explicitially set in the `cra-config.yaml`. |

## CRA Config YAML
The CRA Config YAML provides a more flexible and comprehensive way to specify the configuration for CRA scans. If this YAML file is provided, the corresponding sections of the YAML file will override the relevant input parameters, allowing for more customized scans.

This YAML file is usually located in your repository beside the code. A list of targets can be provided, and CRA will execute a scan against each one. Below is a sample of the YAML file:

```yaml
version: "v1"
CRA_TARGETS:
  - CRA_TARGET: "" # Target directory for CRA scan. If not provided, the CRA Scan will not be run.
    TOOLCHAIN_ID: "" # Optional: The ID of the toolchain to get the profile from. Must be from an account in which the apikey has access to. If not provided, Environment Variable TOOLCHAIN_ID value will be used.
    CRA_ENVIRONMENT_VARIABLES:  # An optional map of environment variables for CRA, where the key is the variable name and value is the value. Useful for providing TF_VARs.
      TF_VAR_sample: "sample value"
      TF_VAR_other:  "another value"
    CRA_IGNORE_RULES_FILE: "" # CRA Ignore file to use. If not provided, it checks the repo root directory for `cra-tf-validate-ignore-rules.json`
    SCC_INSTANCE_ID: "" # The SCC instance ID to use to download profile for CRA scan. If not provided, a default global value will be used.
    SCC_REGION: "" # The IBM Cloud region that the SCC instance is in. If not provided, a default global value will be used.
    PROFILE_ID: "" # The Profile ID input for CRA SCC scan. Ensure to use a US-specific ID. If not provided, a default global value will be used.
```

**Note:** If a parameter is omitted from a section in the YAML file,
the corresponding input value will be used as a fallback.
This allows you to use the YAML file to override input values for specific scans,
while still maintaining the ability to set defaults using the input parameters.
This means that even when the YAML file is in use,
input parameters aren't entirely ignored but serve as default values when a parameter is missing in the YAML,
except `CRA_ENVIRONMENT_VARIABLES` and `CRA_IGNORE_RULES_FILE` which will not take defaults from the inputs.

For example, if you do not provide the `SCC_INSTANCE_ID` in the YAML file,
the action will use the `instanceID` from the input parameters.
This provides flexibility and ensures that you have a backup plan for any missing configuration in the YAML file.
