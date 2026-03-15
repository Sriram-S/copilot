---
title: "Module 08 — CI/CD & Reporting Automation"
module: 8
tags: [ci-cd, github-actions, reporting, pipelines, automation, dashboards]
---

# Module 08 — CI/CD & Reporting Automation

## Learning Objectives

- Generate production-ready GitHub Actions workflows with Copilot
- Build automated reporting: test dashboards, PR annotations, Slack alerts
- Use Copilot to debug failing pipeline jobs
- Automate changelog, release notes, and PR descriptions
- Set up Dependabot and security scanning with Copilot's help

---

## 8.1 Generating a Complete CI Pipeline

Use the `.github/prompts/generate-ci-pipeline.prompt.md` file in this repo:

1. Open Copilot Chat → Agent Mode
2. Run: `Use the prompt at .github/prompts/generate-ci-pipeline.prompt.md`

Or inline:
```
Generate a GitHub Actions CI pipeline for a Python FastAPI project with these jobs:
1. lint (ruff + mypy)  
2. unit-tests (pytest, parallel matrix: Python 3.11, 3.12)
3. integration-tests (requires postgres service container)
4. build-docker-image (multi-stage, push to GHCR)
5. deploy-staging (fly.io, requires manual approval for production)

Requirements:
- All 3rd party actions pinned to SHA
- Test results as PR annotations via dorny/test-reporter
- Slack notification on failure
- Dependabot config for Actions updates
```

---

## 8.2 Anatomy of a Copilot-Generated Workflow

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main]
  pull_request:

permissions:
  contents: read
  checks: write      # required for test-reporter
  pull-requests: write

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"
          cache: "pip"

      - name: Install dependencies
        run: pip install -r requirements-dev.txt

      - name: Run ruff
        run: ruff check .

      - name: Run mypy
        run: mypy src/

  unit-tests:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ["3.11", "3.12"]
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}
          cache: "pip"

      - name: Install dependencies
        run: pip install -r requirements-dev.txt

      - name: Run tests
        run: |
          pytest tests/unit/ \
            --junitxml=test-results/unit-${{ matrix.python-version }}.xml \
            --cov=src \
            --cov-report=xml

      - name: Publish test results
        uses: dorny/test-reporter@31a54ee7ebcacc03a09ea97a7e5465a47b84aea5  # v1.9.1
        if: always()
        with:
          name: Unit Tests (Python ${{ matrix.python-version }})
          path: test-results/*.xml
          reporter: java-junit

      - name: Write job summary
        if: always()
        run: |
          echo "## Test Results" >> $GITHUB_STEP_SUMMARY
          echo "Python: ${{ matrix.python-version }}" >> $GITHUB_STEP_SUMMARY
          pytest tests/unit/ --tb=no -q 2>&1 | tail -5 >> $GITHUB_STEP_SUMMARY

      - name: Upload coverage
        uses: codecov/codecov-action@v4
        with:
          files: coverage.xml
```

---

## 8.3 Debugging Failing Pipelines with Copilot

When a CI job fails, bring the logs into Copilot:

### In VS Code
1. Open `@terminal` in Copilot Chat
2. Paste the GitHub Actions log excerpt
3. Type: `@terminal Why is this CI job failing and how do I fix it?`

### In Copilot Chat (direct)
```
This GitHub Actions job is failing. Here is the relevant log output:

[paste log lines]

1. What is the root cause?
2. How do I fix it?
3. Is this a flaky issue or a real failure?
```

---

## 8.4 Automated Test Reporting

### PR Annotations (Zero Configuration)

With `dorny/test-reporter`, test failures appear directly in PR diffs — no dashboard needed.

### HTML Report Generation

```yaml
- name: Generate HTML report
  if: always()
  run: pytest --html=reports/test-report.html --self-contained-html

- name: Upload report
  uses: actions/upload-artifact@v4
  if: always()
  with:
    name: test-report
    path: reports/test-report.html
    retention-days: 30
```

### Slack Failure Notifications

```yaml
- name: Notify Slack on failure
  if: failure()
  uses: slackapi/slack-github-action@v1.27.0
  with:
    payload: |
      {
        "text": "❌ CI Failed on `${{ github.ref }}`",
        "blocks": [
          {
            "type": "section",
            "text": {
              "type": "mrkdwn",
              "text": "*Pipeline Failed*\nRepo: ${{ github.repository }}\nBranch: `${{ github.ref_name }}`\n<${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}|View Run>"
            }
          }
        ]
      }
  env:
    SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

---

## 8.5 Automating PR Descriptions

```
In Copilot Chat (Agent Mode):
Generate a PR description for the changes in this branch.
Include:
- Summary of what changed and why
- List of files modified
- Test coverage impact
- Any breaking changes
- How to test this locally

Use the conventional commits in the git log to inform the description.
```

---

## 8.6 Automating Changelogs

```
Generate a CHANGELOG.md entry for this release.
Use the git log from the last tag to HEAD.
Format: Keep a Changelog (https://keepachangelog.com)
Group by: Added, Changed, Fixed, Deprecated, Security
```

---

## 8.7 Copilot-Generated Dependabot Config

```
Generate a Dependabot configuration that:
- Updates GitHub Actions weekly (Monday 9am UTC)
- Updates pip dependencies weekly
- Groups all minor/patch pip updates into one PR
- Keeps major updates as individual PRs
- Labels all PRs with 'dependencies'
- Sets reviewer to @automation-team
```

---

## 8.8 Security Scanning in CI

```
Add a security scanning step to the CI pipeline that:
1. Runs pip-audit for Python dependency vulnerabilities
2. Runs bandit for Python SAST
3. Runs CodeQL analysis (use the GitHub-provided action)
4. Fails the build on HIGH or CRITICAL vulnerabilities
5. Creates a GitHub Security Advisory for each finding
```

---

## Module Checkpoint

- [ ] Generated a complete multi-job CI pipeline using Copilot
- [ ] Set up PR annotations with `dorny/test-reporter`
- [ ] Added a Job Summary step to your pipeline
- [ ] Used Copilot Chat to diagnose a failing CI job
- [ ] Set up Slack failure notifications
- [ ] Generated a PR description using Agent Mode
- [ ] Configured Dependabot using Copilot

**Next**: [Module 09 — Multi-Session Strategies](./MODULE-09-multi-session.md)
