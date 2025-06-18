# CI/CD Pipelines and Build Automation

This project utilizes a collection of reusable GitHub Actions and workflow YAML files to automate build, test, and deployment processes across multiple technology stacks (Docker, Maven, NPM, Flutter, Android).

## Table of Contents

- [Overview](#overview)
- [Workflows](#workflows)
  - [Docker Build and Push](#docker-build-and-push)
  - [NPM Build and Deploy](#npm-build-and-deploy)
  - [Maven Build and Deploy](#maven-build-and-deploy)
  - [Flutter Build and Deploy](#flutter-build-and-deploy)
  - [Android App Bundle Upload](#android-app-bundle-upload)
- [Action Inputs and Secrets](#action-inputs-and-secrets)
- [Usage](#usage)
- [Best Practices](#best-practices)
- [Troubleshooting](#troubleshooting)
- [License](#license)

## Overview

This repository provides build and deployment automation for:

- Containerized applications (Docker)
- Node.js/NPM projects
- Java/Maven projects
- Flutter mobile projects
- Android application bundle delivery

Each workflow is modular and can be invoked using `workflow_call` for easy composition and reuse.

## Workflows

### Docker Build and Push

Automates building and pushing Docker images to a specified registry.

**Key Features:**

- Manages Docker registry authentication securely
- Caches build artifacts
- Supports SNAPSHOT and release images

**Typical Inputs:**

- `release`: If true, creates a release and latest tag; else, creates a SNAPSHOT tag
- `cache-key`: Key for storing/restoring build cache

**Required Secrets:**

- `registry_username`
- `registry_token`

### NPM Build and Deploy

Builds and deploys Node.js packages.

**Key Features:**

- Checks out the specified repo/branch
- Sets up Node.js environment
- Can use SonarQube for code analysis

**Typical Inputs:**

- `repository`, `branch`, `path`

**Required Secrets:**

- `sonar_token`
- `deploy_github_token`

### Maven Build and Deploy

Automates Maven builds for Java projects.

**Key Features:**

- Configurable Maven goals, Java version, and distribution
- Multi-branch, multi-repo support
- Output cache key for dependency/job chaining

**Typical Inputs:**

- `repository`, `branch`, `maven_goal`, `maven_version`, `java_version`, `runner`

**Required Secrets:**

- `sonar_token`
- `teams_webhook_url`
- `deploy_github_token`

### Flutter Build and Deploy

Automates Flutter app building and deployment.

**Key Features:**

- Supports repository/branch targeting
- Can build for iOS and/or Android
- Handles release versioning and bundle identifiers

**Typical Inputs:**

- `repository`, `branch`, `working-directory`, `release_version`, `ios_bundle_id`, `android_bundle_id`, `profile_name`, `environments`

**Required Secrets:**

- `sonar_token`
- `IOS_DISTRIBUTION_CERTIFICATE_BASE64` & `IOS_DISTRIBUTION_CERTIFICATE_PASSWORD` (for iOS releases)

### Android App Bundle Upload

Uploads Android App Bundle (`.aab`) files to Google Play Store using the Play Developer API.

**Key Inputs:**

- `service_account_json`: Base64-encoded service account credentials
- `package_name`: Android package name
- `aab_file_path`: Path to .aab file
- `track`: Release track (e.g., internal, alpha, beta, production, rollout)
- `release_status`: Release status (e.g., draft, completed, halted, inProgress)

## Action Inputs and Secrets

Each workflow or composite action defines its own set of required `inputs` and `secrets`. Refer to the specific workflow YAML for details and required variables.

- **Inputs** are passed via the workflow call and allow customization per run.
- **Secrets** are managed via your repository or organization settings in GitHub and should never be hardcoded.

## Usage

To use a workflow in your repository:

1. Reference it via `workflow_call` in your workflow file.
2. Pass required `inputs` and configure necessary `secrets`.
3. Ensure that the correct runner, tools, and environment variables are available as specified in each workflow.

_For example:_


yaml jobs: build: uses: ./path/to/docker-build.yaml with: cache-key: my-cache-key release: true secrets: registry\_username: **{{ secrets.**REGISTRY\_USERNAME }} registry\_token:{{ secrets.REGISTRY\_TOKEN }}


## Best Practices

- **Keep secrets secure:** Always use encrypted secrets and avoid logging sensitive values.
- **Parameterize builds:** Use workflow `inputs` to customize build steps for different branches or releases.
- **Monitor builds:** Integrate notifications (e.g., Teams webhook) for build status.
- **Cache dependencies:** Utilize GitHub Actions cache to speed up repeated builds.

## Troubleshooting

- Ensure all referenced secrets are available and correctly named.
- Check workflow run logs for detailed error messages.
- Validate repository and branch references in your workflow `inputs`.
- Test locally (where possible) before pushing changes to production branches.

## License

Distributed under the MIT License. See [LICENSE](LICENSE) for details.
