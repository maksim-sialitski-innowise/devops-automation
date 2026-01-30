# DevOps Automation

A collection of DevOps automation workflows and configurations focusing on CI/CD pipelines using GitHub Actions, reusable workflows, and DevOps best practices.

## 🚀 Quick Start

### Development / Usage

Clone the repository:

git clone https://github.com/maksim-sialitski-innowise/devops-automation.git

# Explore the workflows
ls .github/workflows

### pr-verification.yml 

Basic PR Validation Runs on every pull request to enforce code quality standards.

#### Checks:

Linting

Build

Unit tests

Dependency lock verification

Branch up-to-date with main

Linear history enforcement

#### uses: 
maksim-sialitski-innowise/devops-automation/.github/workflows/pr-verification.yml@main with: node-version: '20'

### verify-label.yml

Integration/E2E Tests Trigger Runs integration tests when PR has the verify label.

#### Features:

Dynamically checks for verify label using GitHub API

Runs integration/E2E tests

Skips gracefully if label not present

#### uses: 

maksim-sialitski-innowise/devops-automation/.github/workflows/verify-label.yml@main with: node-version: '20'

### publish-label.yml

Release Candidate Creation Creates release candidate when PR has the publish label.

#### Key Functions:

Blocks publishing if version already exists on npm

Creates dev version with suffix: 1.0.0-dev-

Builds and uploads artifacts for verification

Extracts clean version (without dev suffix) for final release

Inputs: with: node-version: '20' add-dev-suffix: true

### main-publish.yml

Final Release Publication Executes final release after PR with publish label is merged to main.

#### Actions:

Publishes package to npm registry

Creates Git tag with version (vX.Y.Z)

Creates GitHub Release with auto-generated notes
