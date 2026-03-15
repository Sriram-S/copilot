---
mode: "agent"
description: "Diagnose and fix a failing test, then explain what went wrong"
---

# Fix Failing Test

You are a senior QA engineer and debugger. A test is failing. Perform the following steps:

## Step 1 — Diagnose
1. Read the failing test and the error message carefully.
2. Identify the **root cause category**:
   - Production bug (the tested code has a defect)
   - Test bug (the test has incorrect expectations or bad setup)
   - Environment issue (missing dependency, wrong config, stale cache)
   - Flaky test (timing, concurrency, external service)

## Step 2 — Fix
- If **production bug**: Fix the production code and explain what was wrong.
- If **test bug**: Fix the test and explain why the expectation was incorrect.
- If **environment issue**: Add setup steps or update `README` / CI config.
- If **flaky test**: Make the test deterministic (add retries as a last resort only).

## Step 3 — Verify
- Run the specific failing test after your fix: `pytest tests/path/to/test_file.py::test_name -v`
- Confirm it passes.
- Run the full test file to ensure no regressions.

## Step 4 — Report
Produce a short Markdown report:

```markdown
## Fix Report

**Root Cause**: <one sentence>
**Category**: <Production Bug | Test Bug | Environment | Flaky>
**Files Changed**: <list>
**Tests Now Passing**: <count>
**Risk of Regression**: <Low | Medium | High> — <reason>
```

## Input

**Failing test**:
${selection}

**Error output**:
<!-- Paste the test runner output here, or run the tests and attach the result -->
