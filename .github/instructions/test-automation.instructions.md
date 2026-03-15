---
applyTo: "tests/**,**/*.test.*,**/*.spec.*"
---

# Test Automation Instructions

These rules apply to **all test files** in this project.

## Structure
- Every test file must import the subject under test from a relative path — no global references.
- Group related tests inside `describe` / `class` blocks named after the feature being tested.
- Test names must be full sentences: `"should return 404 when user id does not exist"`.

## Coverage Requirements
- Aim for ≥ 80 % line coverage on all production code.
- Every public API method must have at minimum:
  - One happy-path test
  - One null/empty-input test
  - One boundary-condition test
  - One error/exception-path test

## Mocking Strategy
- Use dependency injection so classes are easily testable.
- Mock at the boundary (HTTP client, DB connection) — never mock internal private methods.
- Name mocks descriptively: `mockUserRepository`, `stubHttpClient`.

## Assertions
- Use the strictest assertion available (`===`, `assertSame`, etc.).
- Prefer single-concept assertions per test — avoid multiple `assert` calls that test different
  behaviours.
- Include a failure message in every assertion to aid debugging.

## Test Data
- Keep test data in `tests/fixtures/` as JSON or YAML.
- Never use production data, PII, or real credentials in fixtures.
- Use factories/builders for complex object graphs rather than inline literals.

## Automation-Specific Rules
- Playwright tests must use the Page Object Model pattern.
- Every Playwright test must clean up (close browser, delete created records) in an `afterEach`.
- Tag slow / flaky tests with `@slow` or `@flaky` so they can be excluded from fast-feedback runs.
