---
title: "Implementing Secret Scanning with git secrets, trufflehog, and Snyk Dependency Checks using GitHub Actions Workflows"
datePublished: Fri Jul 14 2023 00:50:35 GMT+0000 (Coordinated Universal Time)
cuid: clk1v4grr000109mp49v3c8xd
slug: implementing-secret-scanning-with-git-secrets-trufflehog-and-snyk-dependency-checks-using-github-actions-workflows
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/d2w-_1LJioQ/upload/0462039432b6a7482701f2988315ddaf.jpeg
tags: github, devops, ci-cd, cybersecurity-1

---

I am learning about the basics of creating a blueprint for a secure CI/CD pipeline. Recently, I had the opportunity to implement secret scanning using git secrets and trufflehog, as well as integrate Snyk for dependency checks, all within GitHub Actions workflows. In this blog post, I will share my experience of implementing these tools and the steps I followed to enhance the security of my projects.

**Part 1: Setting Up Secret Scanning with Git Secrets**

To start, I wanted to ensure that my codebase was free from accidental exposure of secrets like API keys or passwords. I decided to use Git secrets to perform secret scanning within my GitHub Actions workflows. Here's how my journey unfolded:

Creating the Workflow File: I navigated to the `.github/workflows` directory in my repository and created a new YAML file, which I named `scanning_git_secrets.yml`, to define my workflow.

Defining the Workflow Trigger: I specified the events that should trigger the secret scanning workflow. I opted to trigger the workflow on both `push` and `pull_request` events to cover various scenarios.

```yaml
name: Scan secrets using Gitleaks

on:
  push:
  pull_request:
```

Setting up the Workflow Steps: I added the necessary steps to install and run git secrets within the workflow. By leveraging the `actions/checkout@v3` action, I fetched the repository code and ran git secrets on it.

```yaml
jobs:
  scan:
    name: gitleaks
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0

      - uses: gitleaks/gitleaks-action@v2
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

Committing and Pushing the Workflow File: Once I was satisfied with the workflow configuration, I saved the workflow file, committed it to my repository, and pushed the changes to trigger the secret scanning workflow.

**Part 2: Identifying Secrets with trufflehog**

In addition to git secrets, I wanted to perform another scan to try a different method and to ensure that I was not missing any potential secrets in my codebase. I decided to incorporate trufflehog into my GitHub Actions workflow. Here's what I did:

Updating the Workflow File: I opened a new file `Trufflehog_scan.yml` workflow file and added the necessary steps to incorporate Trufflehog.

```yaml
name: Scan secrets Trufflehog 
on: [pull_request]
jobs:
  ScanSecrets:
    name: Scan secrets
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          fetch-depth: 0
          ref: ${{ github.head_ref }}
      - name: trufflehog-actions-scan
        uses: edplato/trufflehog-actions-scan@master
```

Committing and Pushing the Workflow File: I saved the workflow file with the trufflehog integration, committed the changes, and pushed them to trigger the updated workflow.

**Part 3: Checking for Vulnerable Dependencies with Snyk**

To further enhance the security of my projects, I decided to incorporate Snyk for dependency checks on Python code. This would help me identify and address any vulnerabilities in my project dependencies. Here's how I integrated Snyk into my GitHub Actions workflow:

Updating the Workflow File: I opened the `snyk_workflow.yml` workflow file and added the necessary steps to incorporate Snyk. The SNYK\_TOKEN needs to be added to the GitHub repository under "Actions secrets and variables" to run the workflow.

```yaml
name: Example workflow for Python using Snyk
on:
  workflow_dispatch:
jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@master
      - name: Run Snyk to check for vulnerabilities
        uses: snyk/actions/python@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        with:
          args: --all-projects --severity-threshold=low --skip-unresolved
```

Committing and Pushing the Workflow File: Once I had integrated Snyk, I saved the workflow file, committed the changes, and pushed them to trigger the updated workflow.

Implementing secret scanning with git-secrets and trufflehog, along with integrating Snyk for dependency checks within GitHub Actions workflows, has significantly improved the quality and security of my codebase. I would like to explore linting using Github actions in the upcoming blogs.