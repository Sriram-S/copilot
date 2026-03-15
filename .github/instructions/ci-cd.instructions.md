---
applyTo: ".github/workflows/**,**/Dockerfile,**/docker-compose*.yml"
---

# CI/CD Automation Instructions

These rules apply to **all workflow, Docker, and infrastructure files**.

## GitHub Actions
- Use `actions/checkout@v4` (or later) for checkout steps.
- Pin third-party actions to their full commit SHA — never use floating tags like `@main`.
- Always define `permissions` at the job level with the minimum required scopes.
- Separate build, test, and deploy into distinct jobs connected via `needs`.
- Upload test result artefacts so they are accessible in the Actions UI after failures.
- Cache dependencies:
  - Python: `actions/cache` on `~/.cache/pip` keyed on `requirements*.txt`
  - Node: `actions/setup-node` with `cache: 'npm'`
  - Java/Maven: `actions/cache` on `~/.m2/repository`

## Secrets & Security
- Reference secrets as `${{ secrets.SECRET_NAME }}` — never echo them in logs.
- Use `GITHUB_TOKEN` for publishing artefacts instead of personal access tokens wherever possible.
- Add `continue-on-error: false` (default) for test jobs so failures block merges.

## Reporting
- Publish JUnit XML reports using `dorny/test-reporter` action for rich PR annotations.
- Send Slack/Teams notifications only on failure using `if: failure()`.
- Include a job summary step that writes a Markdown summary to `$GITHUB_STEP_SUMMARY`.

## Docker
- Use multi-stage builds to keep production images lean.
- Pin base image digests, not floating tags.
- Run containers as non-root users.
- Scan images with `docker scout` or `trivy` in CI before pushing.
