# Module 12: CI/CD Integration

## Learning Objectives

- Generate production-ready GitHub Actions and GitLab CI workflows with Copilot
- Configure test sharding, parallelism, and artifact management
- Use Copilot to analyze CI failures and suggest fixes
- Integrate Copilot into code review workflows via GitHub Actions

---

## 12.1 Generating CI/CD Workflows with Copilot

### Complete GitHub Actions Workflow

```
Generate a production-grade GitHub Actions workflow for our Playwright test suite.

Requirements:
- Trigger: push to main, pull_request to main
- Environment matrix: dev (on PR), staging (on push to main)
- Sharding: 4 parallel shards for E2E tests
- Browser matrix: Chromium only (Firefox and WebKit on nightly schedule)
- Caching: node_modules and Playwright browsers
- Artifacts:
  - Upload HTML report on all runs
  - Upload screenshots + videos only on failure
  - Upload Allure results for report aggregation
- Test categories:
  - Smoke: runs on every PR (fast, ~2 min)
  - Regression: runs on push to main
  - Performance: runs nightly
- Notifications: Slack notification on failure (use SLACK_WEBHOOK secret)
- Test summary in PR comment with pass/fail counts

Output: .github/workflows/playwright.yml
```

### Resulting workflow structure:

```yaml
name: Playwright Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 2 * * *'  # Nightly at 2 AM UTC

jobs:
  smoke-tests:
    runs-on: ubuntu-latest
    # Fast feedback on PRs
    
  e2e-tests:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        shard: [1, 2, 3, 4]
    # Full parallel E2E on main
    
  nightly-full:
    runs-on: ubuntu-latest
    if: github.event_name == 'schedule'
    strategy:
      matrix:
        browser: [chromium, firefox, webkit]
    # Cross-browser nightly
```

---

## 12.2 Test Result Analysis in CI

### Posting Test Results to PRs

```
Generate a GitHub Actions workflow step that:
1. Parses the Playwright JSON test results
2. Posts a comment to the PR with:
   - Total tests run
   - Pass/fail/skip counts
   - List of failed tests with links to trace viewer
   - Comparison with previous run (is it better or worse?)

Use the GitHub Actions API and the GITHUB_TOKEN secret.
```

### Auto-Creating GitHub Issues for Failures

```
Generate a GitHub Actions step that:
- Runs after the test job
- For each failed test:
  1. Checks if a GitHub issue already exists for this test failure
  2. If not, creates a new issue with:
     - Title: "Test Failure: [test name]"
     - Body: failure message, stack trace, affected file, link to workflow run
     - Labels: "test-failure", "automated"
     - Assignee: the PR author
  3. If yes, updates the existing issue with the new failure count

Use octokit/rest.js
```

---

## 12.3 Using Copilot to Debug CI Failures

### The CI Debug Loop

When tests fail in CI, use this Copilot workflow:

**Step 1: Paste the failure output**
```
Our GitHub Actions run failed. Here's the output:

[Paste CI log here]

Analyze:
1. Which tests failed?
2. What is the root cause of each failure?
3. Is this a code issue, environment issue, or flaky test?
4. What is the priority fix for each?
```

**Step 2: Get the specific fix**
```
For the failure "TimeoutError on checkout page", apply this fix:
[Copilot's suggested fix from Step 1]

Show me the exact diff to apply to #file:tests/checkout.spec.ts
```

**Step 3: Verify the fix**
```
After applying the fix, what CI configuration changes should I make to:
1. Prevent this class of failure in the future
2. Get faster feedback when similar issues occur
3. Add better logging to diagnose similar issues
```

---

## 12.4 Docker Integration

```
Generate a Dockerfile and docker-compose.yml for running our Playwright tests 
in a containerized environment.

Requirements:
- Base image: mcr.microsoft.com/playwright:v1.44.0-jammy
- Install our dependencies
- Support running tests with environment variables for base URL, credentials
- Support mounting test results out of container
- Support running specific test tags: docker run ... --grep @smoke

Also generate a .dockerignore file.

Output:
- Dockerfile.test
- docker-compose.test.yml
- .dockerignore
```

---

## 12.5 GitLab CI Integration

```
Generate a GitLab CI pipeline configuration for our Playwright test suite.

Mirror this GitHub Actions workflow: #file:.github/workflows/playwright.yml

Use GitLab-native features:
- Parallel matrix jobs for sharding
- GitLab artifacts for test results
- GitLab Pages for Allure reports
- GitLab environments for dev/staging/production
- Merge request approval rules based on test results

Output: .gitlab-ci.yml
```

---

## 12.6 Automated Test Impact Analysis

```
Generate a GitHub Actions workflow that:

1. On every PR, determines WHICH tests need to run based on changed files
2. Uses the git diff to find modified source files
3. Maps source files to test files using our directory convention:
   - src/pages/CheckoutPage.ts -> tests/checkout.spec.ts
   - src/api/users.ts -> tests/api/users.spec.ts
4. Runs only the tests that are likely to be affected
5. Always runs @smoke tests regardless of what changed
6. Runs the full suite on push to main

This "test impact analysis" reduces PR feedback time from 15 min to ~3 min.
```

---

## 12.7 Copilot in GitHub Pull Request Reviews

### Automated Test Quality Review

```yaml
# .github/workflows/copilot-review.yml
name: Copilot Test Quality Review

on:
  pull_request:
    paths:
      - 'tests/**'
      - 'src/pages/**'

jobs:
  review:
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write
    steps:
      - uses: actions/checkout@v4
      - name: Review test quality with Copilot
        # Copilot workspace can review changed test files
        # and post quality feedback as a PR review comment
```

Use Copilot in the PR review:
```
Review the test files changed in this PR. 
For each changed file:
1. Check for missing negative test cases
2. Identify hardcoded values that should use fixtures
3. Find missing waits that could cause flakiness
4. Assess assertion quality (too broad? too narrow?)
Post a review with line-level comments.
```

---

## 12.8 Performance Regression in CI

```
Generate a GitHub Actions workflow that:

1. Runs Lighthouse performance audits on our key pages after deployment
2. Compares results against baseline (stored in GitHub Gist or S3)
3. Fails the pipeline if performance degrades more than 10% for:
   - First Contentful Paint (FCP)
   - Largest Contentful Paint (LCP)  
   - Total Blocking Time (TBT)
   - Cumulative Layout Shift (CLS)
4. Posts a performance report as a PR comment
5. Updates the baseline on push to main

Use: lighthouse-ci (lhci) CLI
Output: .github/workflows/performance.yml, lighthouserc.json
```

---

## Knowledge Check

1. How do you configure test sharding in GitHub Actions to parallelize a Playwright suite across 4 machines?
2. What artifacts should you always upload in a CI test workflow?
3. How can you use test impact analysis to reduce CI feedback time?

---

## Exercises

**Exercise 12.1:** Use Copilot to generate a complete GitHub Actions workflow for your test project. Run it and fix any issues Copilot helps you identify.

**Exercise 12.2:** Paste a CI failure log into Copilot Chat and follow the 3-step debug loop. Measure how long it takes vs. debugging manually.

**Exercise 12.3:** Generate a Docker configuration for running your tests in a container. Verify it works with `docker compose up`.

---

## Next Module

➡️ [Module 13: Advanced & Latest Features](../module-13/README.md)
