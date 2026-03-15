---
title: "Module 07 — Test Automation with Copilot"
module: 7
tags: [test-automation, unit-tests, integration-tests, e2e, playwright, mutation-testing]
---

# Module 07 — Test Automation with Copilot

## Learning Objectives

- Generate unit, integration, E2E, and mutation tests with Copilot
- Build a complete test pyramid in under 30 minutes per feature
- Use Copilot to diagnose and fix flaky tests
- Achieve meaningful coverage — not coverage theatre
- Apply TDD with Copilot as your coding partner

---

## 7.1 The Test Pyramid with Copilot

```
        ┌─────────────┐
        │   E2E Tests  │   ~10%   Playwright/Selenium
        │    (slow)    │          Copilot writes: page objects, full flows
        └──────┬───────┘
               │
        ┌──────┴───────┐
        │  Integration  │  ~20%   API tests, DB tests
        │    Tests      │         Copilot writes: API clients, fixtures
        └──────┬────────┘
               │
        ┌──────┴────────┐
        │  Unit Tests   │  ~70%   Pure logic, services, utils
        │   (fast)      │         Copilot writes: test cases, mocks, assertions
        └───────────────┘
```

---

## 7.2 Unit Tests — Copilot as TDD Partner

### Step 1: Write the Interface First

```python
# src/pricing/discount_engine.py
from dataclasses import dataclass
from enum import Enum

class CustomerTier(Enum):
    STANDARD = "standard"
    SILVER = "silver"
    GOLD = "gold"
    PLATINUM = "platinum"

@dataclass
class DiscountResult:
    original_price: float
    discount_rate: float
    final_price: float
    applied_rule: str

def calculate_discount(
    original_price: float,
    tier: CustomerTier,
    coupon_code: str | None = None
) -> DiscountResult:
    """Calculate tiered discount with optional coupon override."""
    ...
```

### Step 2: Ask Copilot for Tests Before Implementation

In Copilot Chat:
```
/tests Generate a complete pytest test suite for the calculate_discount function.
Cover: all 4 tiers, valid coupon codes, invalid coupon codes, zero price,
negative price, price at exactly 0 after discount.
```

### Step 3: Run the (Failing) Tests

```bash
pytest tests/pricing/test_discount_engine.py -v
# All tests fail — that's correct for TDD
```

### Step 4: Ask Copilot to Implement

```
Now implement the calculate_discount function to make all tests pass.
Use the test cases as the specification.
```

---

## 7.3 Integration Tests — API Layer

### Generating FastAPI Integration Tests

```python
# Copilot Chat prompt:
"""
Generate pytest integration tests for a FastAPI endpoint POST /api/v1/orders.

Request body: { "user_id": UUID, "items": [{"product_id": UUID, "quantity": int}] }
Success response: 201, body: { "order_id": UUID, "status": "pending", "total": float }

Test scenarios to cover:
- Valid order creation
- Missing user_id (422)
- Empty items list (422)
- Invalid product_id format (422)
- User not found (404)
- Product out of stock (409)
- Unauthenticated request (401)

Use: pytest, httpx.AsyncClient, pytest-asyncio, factory_boy for test data
"""
```

### Integration Test Pattern

```python
import pytest
from httpx import AsyncClient
from app.main import app

@pytest.fixture
async def client():
    async with AsyncClient(app=app, base_url="http://test") as client:
        yield client

@pytest.fixture
def valid_order_payload(user_factory, product_factory):
    user = user_factory.create()
    product = product_factory.create(stock=10)
    return {
        "user_id": str(user.id),
        "items": [{"product_id": str(product.id), "quantity": 2}]
    }

async def test_create_order_returns_201_for_valid_request(
    client: AsyncClient,
    valid_order_payload: dict,
    auth_headers: dict
):
    # Arrange — done via fixtures

    # Act
    response = await client.post(
        "/api/v1/orders",
        json=valid_order_payload,
        headers=auth_headers
    )

    # Assert
    assert response.status_code == 201
    body = response.json()
    assert body["status"] == "pending"
    assert "order_id" in body
    assert body["total"] > 0
```

---

## 7.4 E2E Tests with Playwright — Page Object Model

### Generating Page Objects

```
Generate a Playwright Python Page Object for the checkout flow:
- Page: CheckoutPage (URL: /checkout)
- Elements: cart summary, delivery address form, payment section, "Place Order" button
- Actions: fill_delivery_address(address), select_payment_method(method), place_order()
- Assertions: verify_order_summary(expected_items), verify_confirmation_shown()
```

### Generated Page Object Pattern

```python
# tests/pages/checkout_page.py
from playwright.async_api import Page, expect

class CheckoutPage:
    def __init__(self, page: Page) -> None:
        self._page = page
        self._place_order_btn = page.get_by_role("button", name="Place Order")
        self._address_form = page.get_by_test_id("delivery-address-form")

    async def navigate(self) -> None:
        await self._page.goto("/checkout")

    async def fill_delivery_address(self, address: dict) -> None:
        await self._address_form.get_by_label("Street").fill(address["street"])
        await self._address_form.get_by_label("City").fill(address["city"])
        await self._address_form.get_by_label("Postcode").fill(address["postcode"])

    async def place_order(self) -> None:
        await self._place_order_btn.click()

    async def verify_order_confirmation(self) -> None:
        await expect(self._page.get_by_role("heading", name="Order Confirmed")).to_be_visible()
```

---

## 7.5 Mutation Testing — Validate Your Tests

Standard coverage tells you lines were executed. **Mutation testing** tells you if your tests
actually *catch bugs*.

### Running Mutation Tests with Copilot

```bash
# Install mutmut
pip install mutmut

# Run mutations on your code
mutmut run --paths-to-mutate=src/pricing/

# Ask Copilot to fix survivors
```

In Copilot Chat, paste surviving mutations:
```
These mutations survived (meaning my tests didn't catch them):
[paste mutmut output]

For each surviving mutation, generate a new test case that would kill it.
```

---

## 7.6 Diagnosing Flaky Tests

Paste a flaky test error in Copilot Chat:
```
This test fails intermittently. Here's the error when it fails:
[paste error]

Here's the test code:
[paste test]

Diagnose the root cause and fix it without adding sleeps or retries.
```

**Common Copilot-diagnosed flaky patterns:**

| Pattern | Copilot Fix |
|---------|------------|
| `time.sleep()` | Replace with polling or event wait |
| Shared global state | Add teardown / use function-scoped fixtures |
| Race condition in async test | Add proper `await` and event synchronisation |
| Hard-coded port | Use `0` for OS-assigned port |
| Timezone-sensitive dates | Freeze time with `freezegun` |

---

## 7.7 Coverage-Driven Test Completion

```bash
# Run tests with coverage
pytest --cov=src --cov-report=term-missing --cov-report=json

# Ask Copilot to fill gaps
```

In Copilot Chat:
```
My coverage report shows these uncovered lines:
[paste coverage/lcov.info or term-missing output]

Generate tests to cover the missing branches. Focus on error paths and 
edge cases — the happy paths are already covered.
```

---

## Module Checkpoint

- [ ] Used TDD with Copilot: wrote tests first, then asked Copilot to implement
- [ ] Generated integration tests for an API endpoint with all status code scenarios
- [ ] Generated a Playwright Page Object and E2E test
- [ ] Ran mutation testing and asked Copilot to kill surviving mutations
- [ ] Diagnosed and fixed a flaky test using Copilot Chat
- [ ] Achieved ≥ 85% coverage on a module using coverage-driven prompting

**Next**: [Module 08 — CI/CD & Reporting Automation](./MODULE-08-cicd-reporting.md)
