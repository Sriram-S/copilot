---
applyTo: "src/api/**,tests/api/**"
---

# API Automation Instructions

These rules apply to **all API source and test files**.

## API Client Design
- Every API client class must accept a `base_url` and `timeout` in its constructor.
- Use `httpx` (Python) or `axios` (Node) — not the raw `fetch` API — for easier test mocking.
- Return typed response objects, not raw dicts/JSON.
- Raise domain-specific exceptions (e.g., `UserNotFoundError`) instead of generic HTTP errors.

## Contract Testing
- Write Pact consumer tests for any service boundary this project owns.
- Provider verification must run in CI against the Pact Broker.

## Performance
- Assert p95 response time ≤ 500 ms in integration tests using `pytest-benchmark` or `k6`.
- Add `@pytest.mark.slow` to any test that runs a load scenario.

## Data Isolation
- Every API test must create its own data and clean it up — no shared global state.
- Use UUID suffixes on resource names to avoid collisions during parallel test runs.

## OpenAPI
- Keep `openapi.yaml` in sync with the implementation — validate it in CI.
- Generate API client stubs from the OpenAPI spec using `openapi-generator`.
