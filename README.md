# common-pipeline-assets

[![semantic-release](https://img.shields.io/badge/%20%20%F0%9F%93%A6%F0%9F%9A%80-semantic--release-e10079.svg)](https://github.com/semantic-release/semantic-release)
[![pre-commit](https://img.shields.io/badge/pre--commit-enabled-brightgreen?logo=pre-commit&logoColor=white)](https://github.com/pre-commit/pre-commit)

A repo to maintain common pipeline assets

## Common pipeline workflows

The GitHub Actions workflows are called from the `.github/workflows` directory.
The workflows use the following keywords and syntax:

- Call the reusable workflow by using the `uses` keyword.
    - Refer to the workflow with `{owner}/{repo}/.github/workflows/{filename}@{ref}`. `{ref}` is a release version, not a branch name, for security reasons. The `renovate` task updates the reference to the most recent semantic version of reusable workflows.
- Use the `secrets: inherit` keyword to pass secrets from the module workflow. The common pipeline workflows use secrets in the [terraform-ibm-modules](https://github.com/terraform-ibm-modules) project.

For example, here's how the `common-terraform-module-ci` workflow is called in the module template.

```yaml
jobs:
  call-terraform-ci-pipeline:
    uses: terraform-ibm-modules/common-pipeline-assets/.github/workflows/common-terraform-module-ci.yml@v1.14.0
    secrets: inherit
```
