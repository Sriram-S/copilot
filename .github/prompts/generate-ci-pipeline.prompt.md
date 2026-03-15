---
mode: "agent"
description: "Generate a complete GitHub Actions CI/CD pipeline for this project"
---

# Generate CI/CD Pipeline

You are a DevOps engineer specialising in GitHub Actions. Generate a **production-ready CI/CD
pipeline** for this project.

## Discovery Steps (do these first)
1. Inspect the repository to determine:
   - Primary language(s) and runtime versions
   - Package manager (pip, npm, maven, gradle)
   - Test framework and how to run tests
   - Any existing workflow files in `.github/workflows/`
   - Docker files present?
   - Deployment target (cloud provider, Kubernetes, serverless)?

## Pipeline Requirements

### Triggers
- `push` to `main` → run full pipeline (build + test + deploy to staging)
- `pull_request` → run build + test only
- `workflow_dispatch` → manual trigger for production deploy with environment approval gate

### Jobs (in order)

1. **lint-and-format** — static analysis, type checking, formatting check
2. **unit-tests** — fast, no external dependencies, parallel matrix if multi-runtime
3. **integration-tests** — spin up services via Docker Compose, run API/DB tests
4. **security-scan** — dependency audit + SAST (CodeQL or Semgrep)
5. **build-artifact** — produce deployable artefact (Docker image, JAR, zip)
6. **deploy-staging** — deploy to staging environment (needs: all test jobs)
7. **e2e-tests** — run Playwright/Selenium against staging
8. **deploy-production** — manual approval gate, then deploy (needs: e2e-tests)

### Quality Gates
- Block merge if unit-tests fail
- Block merge if security-scan finds HIGH/CRITICAL vulnerabilities
- Publish test results as PR annotations (dorny/test-reporter)
- Write job summary to `$GITHUB_STEP_SUMMARY`

### Notifications
- On pipeline failure: post to Slack `#automation-alerts`
- On successful production deploy: post to Slack `#deployments`

## Output
Create the file `.github/workflows/ci-cd.yml`. Then summarise what each job does and any
manual steps needed (e.g., adding Slack webhook secret).
