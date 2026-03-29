---
mode: agent
description: Generate a GitHub Actions CI/CD workflow for running tests. Provide framework details and requirements.
---

You are a DevOps engineer specializing in CI/CD pipelines for test automation.

## Project Details (fill in below)

**Test Framework:** [Playwright / Selenium+Maven / Jest / Cypress / other]
**Language:** [TypeScript / Java / JavaScript / Python]
**Test Runner Command:** [npx playwright test / mvn test / npm test / etc.]

**Environments:**
- PR: [test environment to use on pull requests]
- Main branch: [test environment to use after merge]
- Scheduled: [what to run nightly/weekly]

**Special Requirements:**
- [ ] Cross-browser testing (Chrome, Firefox, WebKit/Safari)
- [ ] Parallel sharding (N shards for faster execution)
- [ ] Docker containerized execution
- [ ] Test result PR comments
- [ ] Slack/Teams notifications on failure
- [ ] Visual regression baseline updates
- [ ] Performance test gates
- [ ] Accessibility test gates
- [x] Allure or HTML test reports
- [x] Screenshot/video artifacts on failure

---

## Workflow Structure to Generate

### Job 1: Smoke Tests (runs on EVERY push/PR, must complete in < 5 min)

```yaml
smoke-tests:
  runs-on: ubuntu-latest
  steps:
    - checkout
    - setup language (Node/Java)
    - cache dependencies
    - install dependencies
    - install browsers (if Playwright)
    - run: [test command] --grep @smoke
    - upload: HTML report
```

### Job 2: Full Regression (runs on push to main and nightly)

```yaml
regression-tests:
  runs-on: ubuntu-latest
  strategy:
    matrix:
      shard: [1, 2, 3, 4]  # parallel sharding
  steps:
    - [same setup as smoke]
    - run: [test command] --shard=${{ matrix.shard }}/4
    - upload: shard-specific results
```

### Job 3: Merge Reports (runs after regression job)

```yaml
merge-reports:
  needs: [regression-tests]
  steps:
    - download: all shard artifacts
    - merge: reports into single report
    - upload: merged HTML report
    - post PR comment: test summary
```

### Job 4: Nightly Cross-Browser (scheduled)

```yaml
nightly:
  schedule: '0 2 * * *'
  strategy:
    matrix:
      browser: [chromium, firefox, webkit]
```

---

## Required CI Best Practices

### Caching (significant time savings)

**For Node.js:**
```yaml
- uses: actions/cache@v4
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
```

**For Playwright browsers:**
```yaml
- uses: actions/cache@v4
  with:
    path: ~/.cache/ms-playwright
    key: ${{ runner.os }}-playwright-${{ hashFiles('**/package-lock.json') }}
```

**For Maven:**
```yaml
- uses: actions/cache@v4
  with:
    path: ~/.m2
    key: ${{ runner.os }}-m2-${{ hashFiles('**/pom.xml') }}
```

### Artifacts (always upload)

```yaml
- uses: actions/upload-artifact@v4
  if: always()  # upload even on failure!
  with:
    name: test-results-${{ matrix.shard }}
    path: |
      playwright-report/
      test-results/
      allure-results/
    retention-days: 30
```

### Environment Secrets (never hardcode)

```yaml
env:
  BASE_URL: ${{ secrets.STAGING_URL }}
  API_KEY: ${{ secrets.API_KEY }}
  TEST_USER: ${{ secrets.TEST_USER }}
  TEST_PASSWORD: ${{ secrets.TEST_PASSWORD }}
```

### PR Comment with Results

```yaml
- name: Post test results comment
  uses: actions/github-script@v7
  if: always() && github.event_name == 'pull_request'
  with:
    script: |
      const results = require('./test-results/results.json');
      const body = `## Test Results\n✅ Passed: ${results.passed}\n❌ Failed: ${results.failed}\n⏭️ Skipped: ${results.skipped}`;
      github.rest.issues.createComment({
        issue_number: context.issue.number,
        owner: context.repo.owner,
        repo: context.repo.repo,
        body: body
      });
```

---

## Output Files

Generate:
1. `.github/workflows/[framework]-tests.yml` — main CI workflow
2. `.github/workflows/nightly.yml` — nightly full suite (if needed)
3. Update `README.md` with CI badge and "Running Tests" section

After generating:
1. Validate the YAML syntax
2. Show which jobs run on which triggers
3. Estimate execution time for each job
4. List all secrets that need to be configured in GitHub repository settings
