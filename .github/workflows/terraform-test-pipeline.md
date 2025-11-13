# Terraform-Module-CI GitHub Action

The Terraform-Module-CI action is a GitHub Action designed to provide continuous integration for Terraform modules. It provides automated build and testing for your Terraform projects.

## Overview

This action operates on workflow calls and is composed of two jobs:

- **CI Pipeline**: This job checks out your repository, sets ownership, installs dependencies, and runs pre-commit against all files.
- **Infrastructure Tests**: This job again checks out your repository, sets ownership, installs dependencies, and runs unit tests.

## Usage

To use this action in your GitHub workflow, you will need to provide input parameters as per your requirements. The following inputs can be configured:

| Input Parameter           | Description                                                                                                                      | Required | Default Value                          |
|---------------------------|----------------------------------------------------------------------------------------------------------------------------------|----------|----------------------------------------|
| `checkout_commit`         | Commit ID you want to checkout.                                                                                                  | Yes      |                                        |
| `tfswitchDir` | Specify the location of a .tf file with the terraform constraint defined to automatically download the latest terraform version in the defined range.   | No       |  Defaults to the repos root level (aka ".")                                      |
| `GITHUB_BASE_REF`         | The target branch merging into.                                                                                                  | Yes      |                                        |
| `GITHUB_HEAD_REF`         | The source branch coming from.                                                                                                   | Yes      |                                        |

## Jobs

Each job executes on the latest Ubuntu runner, and uses a predefined container image `icr.io/goldeneye_images/goldeneye-ci-image:stable`.

### CI Pipeline Job

This job provides a basic CI pipeline, which includes steps like repository checkout, setting file ownership, dependency installation, and running pre-commit.

### Infrastructure Tests Job

This job primarily focuses on running infrastructure tests. It checks out the repository, sets ownership, installs dependencies, and runs unit tests.

## Secrets

The action uses secrets that need to be configured at the ORG level:

| Secret Name              | Description                                |
|--------------------------|--------------------------------------------|
| `IBMCLOUD_API_KEY`       | IBM Cloud API Key.                         |
