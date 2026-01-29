DevOps Automation - Reusable GitHub Actions Workflows
📁 Overview
This repository contains reusable GitHub Actions workflows for CI/CD pipelines.

🚀 Available Workflows
1. pr-verification.yml - Basic PR Validation
Runs on every pull request to enforce code quality standards.

Checks:

Linting

Build

Unit tests

Dependency lock verification

Branch up-to-date with main

Linear history enforcement

Usage:

uses: maksim-sialitski-innowise/devops-automation/.github/workflows/pr-verification.yml@main
with:
  node-version: '20'
  
  
2. verify-label.yml - Integration/E2E Tests Trigger
Runs integration tests when PR has the verify label.

Features:

Dynamically checks for verify label using GitHub API

Runs integration/E2E tests

Skips gracefully if label not present

Usage:

uses: maksim-sialitski-innowise/devops-automation/.github/workflows/verify-label.yml@main
with:
  node-version: '20'
  
3. publish-label.yml - Release Candidate Creation
Creates release candidate when PR has the publish label.

Key Functions:

Blocks publishing if version already exists on npm

Creates dev version with suffix: 1.0.0-dev-<short-sha>

Builds and uploads artifacts for verification

Extracts clean version (without dev suffix) for final release

Inputs:
with:
  node-version: '20'
  add-dev-suffix: true
  
4. main-publish.yml - Final Release Publication
Executes final release after PR with publish label is merged to main.

Actions:

Publishes package to npm registry

Creates Git tag with version (vX.Y.Z)

Creates GitHub Release with auto-generated notes

Required Secrets:

NPM_TOKEN: npm authentication token

GH_TOKEN: GitHub token for release creation

🛠️ Setup Instructions
1. Create npm Token
Go to npmjs.com → Access Tokens → Create New Token

Select: Granular Token, Read and Write access

Enable: "Bypass 2FA" if 2FA is enabled on your npm account

2. Add Secrets to Repository
Go to your repository Settings

Select "Secrets and variables" → "Actions"

Click "New repository secret"

Add NPM_TOKEN with your npm token value

3. Configure Branch Protection (Optional)
You can configure branch protection using GitHub CLI:

bash
gh api repos/OWNER/REPO/branches/main/protection \
  -X PUT \
  -f required_status_checks='[{"context":"pr-verification"}]' \
  -f required_linear_history=true
  
📦 Versioning Strategy
Pre-merge (PR with publish label)
Version: 1.0.0-dev-abc1234 (with commit SHA suffix)

Purpose: Testing and verification

Post-merge (After PR merged to main)
Version: 1.0.0 (clean version)

Action: Published to npm, tagged in Git, GitHub Release created

🔧 Troubleshooting
Common Issues
publish job skipped after merge
Check the if: condition in your ci-cd.yml file.

npm publish fails with 403 error
Verify that:

NPM_TOKEN has publish permissions

2FA bypass is enabled if your npm account has 2FA

Token is not expired

Integration tests not running
Ensure:

PR has verify label

Workflow uses dynamic label checking (not just initial check)

Version already exists error
Increment the version in package.json before adding the publish label.

Debug Workflow
Add this debug step to your workflow:

yaml
- name: Debug Context
  run: |
    echo "Event: ${{ github.event_name }}"
    echo "Ref: ${{ github.ref }}"
    echo "Commit message: ${{ github.event.head_commit.message }}"
📝 Notes
This repository is part of a DevOps CI/CD challenge implementation. Workflows are designed to be generic and reusable across JavaScript/TypeScript projects.

Repository structure:

.github/workflows/
├── pr-verification.yml
├── verify-label.yml
├── publish-label.yml
└── main-publish.yml

For questions or issues, check the workflow logs and ensure all required secrets are properly configured.
