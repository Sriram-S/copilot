---
title: "Lab 05 — CI/CD Pipeline Builder"
lab: 5
duration: "60 minutes"
tags: [ci-cd, github-actions, reporting, pipeline, automation]
---

# Lab 05 — CI/CD Pipeline Builder

## Objective

Build a production-ready CI/CD pipeline using the prompt file from this repo
and then debug an intentionally broken pipeline.

---

## Part A — Generate Your Pipeline (20 min)

### Run the Prompt

In Agent Mode:
```
Use the prompt at .github/prompts/generate-ci-pipeline.prompt.md
to generate a complete CI/CD pipeline for this project.

Additional context:
- Language: Python
- Test framework: pytest with allure reporting
- Coverage threshold: 85%
- Docker: yes, build and push to GHCR
- Deployment target: fly.io (staging only, no prod deploy needed)
- Notify on failure: Slack webhook
```

### Inspect the Generated Pipeline

After generation, review the workflow file and verify:
- [ ] All actions pinned to SHA (no floating tags)
- [ ] `permissions` block present
- [ ] Caching configured for pip
- [ ] Test results uploaded as artifact
- [ ] Job summary written
- [ ] Slack notification only on `if: failure()`

If any of these are missing, ask Copilot to fix them.

---

## Part B — Validate Locally (15 min)

### Install `act` for local GitHub Actions testing

```bash
# macOS
brew install act

# Linux
curl https://raw.githubusercontent.com/nektos/act/master/install.sh | sudo bash
```

### Run the pipeline locally

```bash
act push --job lint
act push --job unit-tests
```

Fix any errors Copilot didn't anticipate using `@terminal fix this CI error`.

---

## Part C — Debug a Broken Pipeline (25 min)

### The Broken Pipeline

Create this intentionally broken workflow. Your job: find and fix all 7 bugs.

```yaml
# Save as .github/workflows/broken.yml
name: Broken Pipeline (Lab 05 Debug Exercise)

on: [push]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@main                    # Bug 1

      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"

      - name: Install deps
        run: pip install pytest                         # Bug 2: missing dev deps

      - name: Run tests
        run: python -m pytest tests/ --junitxml=results.xml

      - name: Upload results
        uses: actions/upload-artifact@v4
        with:
          name: test-results
          path: test-results.xml                       # Bug 3: wrong path

  deploy:
    runs-on: ubuntu-latest                             # Bug 4: missing needs:
    steps:
      - name: Deploy to production
        run: echo "Deploying ${{ secrets.DEPLOY_TOKEN }}"  # Bug 5: secret exposed
        env:
          TOKEN: ${{ secrets.DEPLOY_TOKEN }}

      - name: Notify success
        if: always()                                   # Bug 6: wrong condition
        uses: slackapi/slack-github-action@v1.27.0
        with:
          payload: '{"text": "Deployed!"}'
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_URL }}

  security:
    runs-on: ubuntu-latest
    steps:
      - name: Scan dependencies
        run: pip-audit --requirement requirements.txt  # Bug 7: file might not exist
```

### Debugging Process

In Copilot Chat:
```
Review the workflow file at #file:.github/workflows/broken.yml
Find all bugs and security issues. For each one:
1. Identify the bug
2. Explain why it's a bug
3. Show the corrected version
```

---

## ✅ Completion Criteria

- [ ] Complete CI pipeline generated with all 6 quality checkboxes passing
- [ ] Pipeline runs successfully with `act` locally
- [ ] All 7 bugs in the broken pipeline identified and fixed
- [ ] Security bug (secret in run command) explained and patched
- [ ] Final broken.yml committed as broken-fixed.yml with changes documented
