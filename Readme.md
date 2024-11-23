## Pushing a Docker Image to AWS ECR from GitHub Actions

This repository provides an example of using GitHub Actions to build a Docker image and push it to the AWS Elastic Container Repository (ECR).

### Usage

To use this repository, you can fork or clone it. When you push a commit to the main branch, it triggers a GitHub Action workflow that builds and pushes the Docker image to your ECR.

#### AWS Setup

If you don't have an ECR repository, you can create one by following the instructions in [Create an ECR Repo](/docs/create_an_ecr_repo.md).

For AWS authentication, instead of storing credentials in GitHub secrets, this workflow uses OpenID Connect (OIDC) to request temporary credentials from AWS.

Additionally, create an IAM role that the GitHub Action can assume to push an image to the ECR. Assign the necessary permissions to this role.

Refer to the following documentation for detailed instructions:

1. [Create an ECR Repo](/docs/create_an_ecr_repo.md)
2. [Setting up an Identity Provider](/docs/setting_up_identity_provider.md)
3. [Create an IAM Role](/docs/create_an_IAM_role.md)

#### GitHub Action Setup

The main part of the workflow is already set up. You need to create secrets for `account_id`, `aws_region`, and `iam_role`, as described in [GitHub Actions Setup](/docs/github_action_workflow.md#necessary-steps).

#### (Optional) Pre-commit Configuration

`Pre-commit` runs pre-defined hooks before changes are committed. This repository uses the following hooks:

- `trailing-whitespace`: Fixes trailing whitespace.
- `check-yaml`: Verifies YAML file syntax.
- `actionlint`: Checks GitHub Actions workflow YAML files.
- `hadolint-docker`: Checks Dockerfiles for best practices.

##### Installation

- Create an environment using the `environment.yml` file:

  ```bash
  conda env create -f environment.yml
  ```

- Alternatively, you can create the environment manually:

  - Create a Conda environment, for example, with Python 3.8:

    ```bash
    conda create -n pre-commit python=3.8
    ```

  - Install `pre-commit`:

    ```bash
    pip install pre-commit
    ```

- Now, install the hooks with:

  ```bash
  pre-commit install
  ```

You are good to go!

##### Skipping Pre-commit Checks

Even after configuring `pre-commit`, you can commit changes without running `pre-commit` checks by using the `--no-verify` argument. For example:

```bash
git commit -m "Commit without pre-commmit" --no-verify
```

