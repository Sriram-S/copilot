---
title: "Module 13 — Capstone Project"
module: 13
tags: [capstone, project, end-to-end, automation-framework, real-world]
---

# Module 13 — Capstone Project

## Overview

You will build a **complete automation framework** for a fictional e-commerce API using every
technique from this course. The project is designed so that Copilot builds ≥ 80% of the code —
your job is to be the skilled architect who guides it.

---

## Project: ShopFlow Automation Framework

**Target application**: ShopFlow — a RESTful e-commerce API (orders, products, users, payments)

**What you will build:**

```
shopflow-automation/
├── .github/
│   ├── copilot-instructions.md       # Already shows your skills
│   ├── instructions/
│   │   ├── test-automation.instructions.md
│   │   └── ci-cd.instructions.md
│   ├── prompts/
│   │   └── (your custom prompts)
│   ├── agents/
│   │   └── (your custom agents)
│   └── workflows/
│       └── ci.yml
├── src/
│   ├── api_client/                   # API client layer
│   ├── page_objects/                 # UI Page Objects (Playwright)
│   └── data_factory/                 # Test data generators
├── tests/
│   ├── unit/                         # Pure logic tests
│   ├── integration/                  # API integration tests
│   ├── e2e/                          # Playwright E2E tests
│   └── performance/                  # k6 performance tests
├── reports/                          # Generated test reports
└── README.md
```

---

## Phase 1: Project Bootstrap (Using Agent Mode)

**Time target**: 30 minutes using Copilot

### Agent Task 1.1: Scaffold the Project

```
Agent Mode prompt:

"Scaffold a Python test automation framework for a REST API with these specs:
- Language: Python 3.12
- Test framework: pytest
- API testing: httpx (async)
- E2E: playwright
- Reporting: allure-pytest
- Data: factory_boy + faker

Create: pyproject.toml, conftest.py, directory structure, .gitignore, 
and a basic README."
```

### Agent Task 1.2: Write the copilot-instructions.md

```
Based on the project you just scaffolded, write a comprehensive 
.github/copilot-instructions.md that captures all the standards
for this automation framework. Include:
- Project context
- Framework conventions (naming, structure, patterns)
- Test data rules
- Assertion standards
- Reporting requirements
```

**Checkpoint**: You have a working project structure. All `pytest` commands succeed (with 0 tests).

---

## Phase 2: API Client Layer (Using Inline + Chat)

**Time target**: 45 minutes using Copilot

### Task 2.1: Generate the API Client

Use **docstring-first development**:

```python
class ShopFlowAPIClient:
    """
    Async HTTP client for the ShopFlow REST API.
    
    Base URL: configured via SHOPFLOW_BASE_URL environment variable
    Auth: Bearer token, set via SHOPFLOW_API_TOKEN env var
    Timeout: 30 seconds default
    
    Endpoints covered:
    - Users: GET /users/{id}, POST /users, PUT /users/{id}, DELETE /users/{id}
    - Products: GET /products, GET /products/{id}, POST /products
    - Orders: POST /orders, GET /orders/{id}, PATCH /orders/{id}/status
    - Payments: POST /payments, GET /payments/{id}
    
    All methods raise ShopFlowAPIError on non-2xx responses.
    All methods are async.
    """
```

### Task 2.2: Generate Unit Tests for the Client

```
/tests Generate a complete pytest test suite for ShopFlowAPIClient.
Mock all HTTP calls using respx. Cover every method, including error paths.
```

**Checkpoint**: `pytest tests/unit/ -v` passes, ≥ 90% coverage on the API client.

---

## Phase 3: Data Factory (Using Example-Continuation)

**Time target**: 30 minutes

### Task 3.1: Create Product Factory

```python
import factory
from faker import Faker

fake = Faker()

# Pattern: factories use factory_boy with realistic data

class UserFactory(factory.Factory):
    class Meta:
        model = dict  # Replace with User dataclass when available
    
    id = factory.LazyFunction(lambda: str(uuid4()))
    email = factory.LazyAttribute(lambda obj: f"{obj.first_name.lower()}.{obj.last_name.lower()}@example.com")
    first_name = factory.Faker("first_name")
    last_name = factory.Faker("last_name")
    created_at = factory.Faker("date_time_this_year", tzinfo=timezone.utc)

# Copilot continues the pattern for ProductFactory, OrderFactory, PaymentFactory
```

---

## Phase 4: Integration Test Suite (Using Agent Mode)

**Time target**: 60 minutes

### Agent Task 4.1: Generate Full Integration Suite

```
"Generate a comprehensive integration test suite for the ShopFlow API.
Use the ShopFlowAPIClient and factories from Phase 2 and 3.

Cover these user journeys:
1. User registration flow (create user, verify exists, update profile)
2. Product catalog browsing (list, filter, get by ID)
3. Happy path order flow (create user → browse → add to cart → place order → confirm)
4. Payment processing (successful payment, declined card, refund)
5. Error flows (not found, validation, auth failure, rate limiting)

Run the tests against a mock API server (use respx for HTTP mocking).
Target: 50+ test cases, all passing."
```

---

## Phase 5: E2E Test Suite (Using Agent Mode + Page Objects)

**Time target**: 45 minutes

### Agent Task 5.1: Generate Page Objects

```
"Generate Playwright Python page objects for the ShopFlow web frontend:
- LoginPage (/login)
- ProductListPage (/products) 
- ProductDetailPage (/products/{id})
- CartPage (/cart)
- CheckoutPage (/checkout)
- OrderConfirmationPage (/orders/{id}/confirmation)

Each page object must include:
- Locators defined as class attributes
- Action methods (click, fill, select)
- Assertion methods that use Playwright's expect()
- Screenshot on failure via fixture"
```

### Agent Task 5.2: Generate E2E Scenarios

```
"Using the page objects you just created, generate E2E tests for:
1. Guest purchase flow
2. Registered user purchase with saved address
3. Failed payment with retry
4. Order cancellation

Follow pytest-bdd Gherkin format."
```

---

## Phase 6: CI/CD Pipeline (Using Prompt File)

**Time target**: 20 minutes

### Task 6.1: Run the CI Pipeline Prompt

```
Use the prompt at .github/prompts/generate-ci-pipeline.prompt.md
to generate the complete CI pipeline for this project.
```

**Expected output**: `.github/workflows/ci.yml` with:
- Lint → unit-tests → integration-tests → E2E → Allure report publish

---

## Phase 7: Performance Tests (Using Chat)

**Time target**: 30 minutes

### Task 7.1: k6 Performance Test

```
Generate a k6 performance test for the ShopFlow order creation endpoint.
Simulate Black Friday traffic:
- Stage 1: Ramp from 0 to 100 VUs over 2 minutes
- Stage 2: Hold at 100 VUs for 5 minutes
- Stage 3: Spike to 500 VUs for 30 seconds (stress test)
- Stage 4: Ramp down to 0 over 1 minute

Thresholds:
- p95 response time < 800ms
- p99 response time < 2000ms
- Error rate < 0.5%

Output: HTML report with charts
```

---

## Phase 8: Final Delivery Report (Agent Mode)

### Agent Task 8.1: Comprehensive Project Report

```
"Generate a comprehensive project delivery report for the ShopFlow 
Automation Framework. Include:
- Executive summary (what was built, why, key metrics)
- Architecture overview with diagram (Mermaid)
- Test coverage summary (all 4 levels of the pyramid)
- CI/CD pipeline description
- How to run locally (step-by-step)
- Known limitations and improvement backlog
- Team usage guide (how to extend the framework)

Save as DELIVERY-REPORT.md at the root of the project."
```

---

## Capstone Acceptance Criteria

You have completed the capstone when:

- [ ] `pytest tests/ -v` produces ≥ 50 passing tests with 0 failures
- [ ] Coverage: ≥ 85% on the API client and data factories
- [ ] CI pipeline runs successfully (at least on `act` local runner or GitHub Actions)
- [ ] At least one Playwright E2E test runs against the mock server
- [ ] `DELIVERY-REPORT.md` exists and is complete
- [ ] `.github/copilot-instructions.md` captures all project conventions
- [ ] At least 2 custom prompt files and 1 custom agent are present

---

## Reflection — What You've Achieved

In this project you used:
- ✅ Inline completions (docstring-first, pattern continuation)
- ✅ Copilot Chat (`/explain`, `/fix`, `/tests`, `/doc`)
- ✅ Agent Mode (multiple autonomous tasks)
- ✅ Custom Instructions (project-wide + scoped)
- ✅ Custom Prompt Files (CI generation, test generation)
- ✅ Custom Agents (if you created your own personas)
- ✅ Multi-session (phases ran as separate sessions)
- ✅ @workspace (navigating unfamiliar code)
- ✅ @terminal (debugging test failures)

**You are now in the top 1% of Copilot users. Welcome.**

---

## What's Next

- Share your `.github/` configuration with your team
- Iterate on your instructions based on real-world usage
- Watch [GitHub's Copilot What's New page](https://github.com/features/copilot/whats-new) for new features
- Contribute improvements back to this course repository
