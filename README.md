DevOps Automation - Reusable GitHub Actions Workflows
📁 Overview
This repository contains reusable GitHub Actions workflows for CI/CD pipelines, designed to be called from other repositories. It follows the principle of DRY (Don't Repeat Yourself) by centralizing common automation patterns.

🚀 Available Workflows
1. pr-verification.yml - Basic PR Validation
Purpose: Runs on every pull request to enforce code quality standards

Checks:

✅ Linting

✅ Build

✅ Unit tests

✅ Dependency lock verification (package-lock.json)

✅ Branch up-to-date with main

✅ Linear history enforcement

Usage:

yaml
uses: maksim-sialitski-innowise/devops-automation/.github/workflows/pr-verification.yml@main
with:
  node-version: '20'
2. verify-label.yml - Integration/E2E Tests Trigger
Purpose: Runs integration tests when PR has the verify label

Features:

🔍 Dynamically checks for verify label using GitHub API

🧪 Runs integration/E2E tests (trivial tests acceptable for demo)

⏭️ Skips gracefully if label not present

Usage:

yaml
uses: maksim-sialitski-innowise/devops-automation/.github/workflows/verify-label.yml@main
with:
  node-version: '20'
3. publish-label.yml - Release Candidate Creation
Purpose: Creates release candidate when PR has the publish label

Key Functions:

🚫 Blocks publishing if version already exists on npm

🏗️ Creates dev version with suffix: 1.0.0-dev-<short-sha>

📦 Builds and uploads artifacts for verification

🔍 Extracts clean version (without dev suffix) for final release

Inputs:

yaml
with:
  node-version: '20'
  add-dev-suffix: true  # Optional: creates dev version
Outputs:

clean-version: Base version without suffix (e.g., 1.0.0)

4. main-publish.yml - Final Release Publication
Purpose: Executes final release after PR with publish label is merged to main

Actions:

📤 Publishes package to npm registry

🏷️ Creates Git tag with version (vX.Y.Z)

🎯 Creates GitHub Release with auto-generated notes

Required Secrets:

NPM_TOKEN: npm authentication token

GH_TOKEN: GitHub token for release creation

🎯 CI/CD Pipeline Integration Example
Calling Repository's ci-cd.yml:
yaml
name: CI/CD Pipeline
on:
  pull_request:
    types: [labeled, unlabeled, opened, synchronize, reopened, closed]
  push:
    branches: [main]

jobs:
  pr-verification:
    if: github.event_name == 'pull_request'
    uses: maksim-sialitski-innowise/devops-automation/.github/workflows/pr-verification.yml@main
  
  integration-tests:
    if: github.event_name == 'pull_request'
    needs: pr-verification
    uses: maksim-sialitski-innowise/devops-automation/.github/workflows/verify-label.yml@main
  
  release-candidate:
    if: |
      github.event_name == 'pull_request' &&
      contains(github.event.pull_request.labels.*.name, 'publish')
    needs: [pr-verification, integration-tests]
    uses: maksim-sialitski-innowise/devops-automation/.github/workflows/publish-label.yml@main
    with:
      add-dev-suffix: true
  
  publish:
    if: github.event_name == 'push' && github.ref == 'refs/heads/main'
    uses: maksim-sialitski-innowise/devops-automation/.github/workflows/main-publish.yml@main
🔐 Required Secrets
In Calling Repository:
yaml
secrets:
  NPM_TOKEN: ${{ secrets.NPM_TOKEN }}          # npm publish token
  GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}    # Auto-provided by GitHub
npm Token Requirements:
Granular Access Token with "Read and Write" permissions

Must have "Bypass two-factor authentication (2FA)" enabled if 2FA is active on npm account

Scoped to specific package or "All packages"

📦 Versioning Strategy
Pre-merge (PR with publish label):
Version: 1.0.0-dev-abc1234 (with commit SHA suffix)

Purpose: Testing and verification

Post-merge (After PR merged to main):
Version: 1.0.0 (clean version)

Action: Published to npm, tagged in Git, GitHub Release created

🛠️ Setup Instructions
1. Create npm Token:
bash
# On npmjs.com → Access Tokens → Create New Token
# Select: Granular Token, Read and Write access
# Enable: "Bypass 2FA" if 2FA is enabled
2. Add Secrets to Repository:
Go to Repository Settings → Secrets and variables → Actions

Add NPM_TOKEN with your npm token

3. Configure Branch Protection:
bash
# Require status checks before merging
gh api repos/OWNER/REPO/branches/main/protection \
  -X PUT \
  -f required_status_checks='[{"context":"pr-verification"}]' \
  -f required_linear_history=true
🔧 Troubleshooting
Common Issues:
Issue	Solution
publish job skipped after merge	Check if: condition in ci-cd.yml
npm publish fails with 403	Verify NPM_TOKEN has publish permissions and 2FA bypass
Integration tests not running	Ensure PR has verify label and workflow uses dynamic label checking
Version already exists error	Increment version in package.json before adding publish label
Debug Workflow:
yaml
# Add debug step
- name: Debug Context
  run: |
    echo "Event: ${{ github.event_name }}"
    echo "Ref: ${{ github.ref }}"
    echo "Commit message: ${{ github.event.head_commit.message }}"
📝 License & Contribution
This repository is part of a DevOps CI/CD challenge implementation. Workflows are designed to be generic and reusable across JavaScript/TypeScript projects.

Repository Structure:

text
.github/workflows/
├── pr-verification.yml     # Basic PR checks
├── verify-label.yml        # Integration tests trigger
├── publish-label.yml       # Release candidate creation
└── main-publish.yml        # Final release publication
For questions or issues, please check the workflow logs and ensure all required secrets are properly configured.
