# Module 10: AI-Powered Test Generation

## Learning Objectives

- Generate complete, runnable test suites from source code
- Use Copilot to discover and fill coverage gaps
- Generate parametrized, data-driven, and property-based tests
- Create test fixtures, factories, and helpers automatically

---

## 10.1 The `/tests` Command in Depth

The `/tests` slash command is the fastest path from production code to test coverage.

### 10.1.1 In VS Code

1. Open any source file
2. Select the code you want to test (a function, a class, or the entire file)
3. In Chat, type: `/tests`
4. Copilot generates a complete test file and offers to create it

### 10.1.2 In IntelliJ IDEA

1. Place cursor inside a class or method
2. Press `Alt+Enter`
3. Select "Generate tests with Copilot"
4. OR use Chat panel with `/tests` slash command

### 10.1.3 Enhancing the `/tests` Command

Add specifics to the slash command:

```
/tests Generate comprehensive unit tests including:
- Happy path with valid inputs
- Boundary conditions (empty, null, max values)
- Error cases (validation failures, exceptions)
- Concurrency scenarios if applicable
Use Jest with TypeScript. Mock external dependencies with jest.mock().
```

---

## 10.2 Test Generation from Specifications

Generate tests directly from requirements, user stories, or API specs.

### 10.2.1 From a User Story

```
Given this user story, generate Playwright tests:

---
User Story: As a registered user, I want to reset my password
so that I can regain access to my account when I forget it.

Acceptance Criteria:
1. User can request a password reset from the login page
2. User receives an email with a reset link (we mock the email in tests)
3. Reset link expires after 24 hours (we mock time in tests)
4. User can set a new password using the reset link
5. Old password no longer works after reset
6. New password must meet complexity requirements
7. Used reset links cannot be reused
---

Generate a complete spec file: tests/e2e/password-reset.spec.ts
Include positive and negative scenarios for each acceptance criterion.
```

### 10.2.2 From an OpenAPI Specification

```
#file:openapi/users-api.yaml

Generate comprehensive API tests for all endpoints defined in this OpenAPI spec.

For each endpoint, generate tests for:
1. Valid request with all required fields -> 2xx response
2. Valid request with optional fields -> 2xx response
3. Missing required fields -> 400/422 response with error details
4. Invalid field types/formats -> 400/422 response
5. Unauthorized request -> 401 response
6. Forbidden request -> 403 response
7. Resource not found (where applicable) -> 404 response
8. Rate limiting scenarios (where documented)

Use: Supertest + Jest + TypeScript
Output: tests/api/ (one file per endpoint group)
```

### 10.2.3 From Database Schema

```
#file:schema.sql

Generate API-level integration tests based on this database schema.

For each entity (table):
1. CRUD operation tests (create, read, update, delete)
2. Constraint validation tests (required fields, unique constraints, FK constraints)
3. Cascade behavior tests where FK relationships exist

Use: TestContainers + JUnit 5 + Spring Boot Test
Output: src/test/java/integration/ (one file per entity)
```

---

## 10.3 Data-Driven Test Generation

### 10.3.1 Generating Test Data Tables

```
Generate a comprehensive data table for testing email validation.

Include valid emails and invalid emails.

Valid cases (should pass):
- Standard format: user@domain.com
- Subdomain: user@sub.domain.com
- Plus addressing: user+tag@domain.com
- Long TLD: user@domain.technology
- Numbers in local part: user123@domain.com

Invalid cases (should fail):
- Missing @ symbol
- Missing domain
- Spaces in email
- Multiple @ symbols
- Leading/trailing dots
- Consecutive dots
- Very long local part (>64 chars)

Format as: Jest describe.each / pytest parametrize / TestNG DataProvider
(specify your framework)
```

### 10.3.2 Generating Faker-Based Factories

```
Generate a complete TestDataFactory using @faker-js/faker for an e-commerce system.

Entities needed:

1. User: { id, email, firstName, lastName, role: 'admin'|'seller'|'buyer', 
           createdAt, address }
2. Product: { id, name, description, price, category, stock, images[], 
              seller: User }
3. Order: { id, buyer: User, items: OrderItem[], status, total, 
            shippingAddress, createdAt }
4. OrderItem: { product: Product, quantity, priceAtPurchase }

Each factory method:
- `create[Entity](overrides?: Partial<Entity>)` - creates one
- `createMany[Entity](count: number, overrides?: Partial<Entity>)` - creates array
- Uses realistic fake data from faker
- Ensures referential integrity (Order.buyer is a valid User)

Output: src/test/factories/TestDataFactory.ts
```

---

## 10.4 Property-Based Test Generation

```
Generate property-based tests for this calculateShipping() function:
#file:src/utils/shipping.ts

Use fast-check (npm package) for TypeScript property-based testing.

Properties to test:
1. Shipping cost is always non-negative
2. Heavier packages always cost >= lighter packages (same dimensions)
3. Larger dimensions always cost >= smaller (same weight)
4. Free shipping threshold: order > $100 always results in 0 shipping cost
5. International shipping always costs more than domestic
6. The function never throws for valid positive inputs

For each property, use fc.property() with appropriate arbitraries.
```

---

## 10.5 Visual Regression Test Generation

```
We use Playwright with visual comparison (@playwright/test's toHaveScreenshot).

Generate visual regression tests for our design system components.

For each component in src/components/, generate a test that:
1. Renders the component in its default state
2. Takes a screenshot named: [ComponentName]-default.png
3. Tests all visual states: hover, focus, disabled, error, loading
4. Tests responsive breakpoints: mobile (375px), tablet (768px), desktop (1440px)
5. Tests dark mode variant if applicable

Output: tests/visual/[ComponentName].visual.spec.ts
Configuration: Update playwright.config.ts to add visual comparison settings
```

---

## 10.6 Accessibility Test Generation

```
Generate accessibility tests for the checkout flow pages.

Pages to test:
#file:src/pages/CartPage.ts
#file:src/pages/CheckoutPage.ts
#file:src/pages/OrderConfirmationPage.ts

Use axe-core with @axe-core/playwright.

For each page, test:
1. No critical/serious axe violations (use `toHaveNoViolations()`)
2. All images have alt text
3. All form inputs have associated labels
4. Keyboard navigation works correctly (Tab order is logical)
5. Color contrast meets WCAG AA standards
6. Screen reader announcements for dynamic content

Output: tests/accessibility/checkout-a11y.spec.ts
```

---

## 10.7 Performance Test Generation

```
Generate k6 performance tests for our REST API.

API documentation: #file:openapi/api.yaml

Generate load profiles for:
1. Smoke test: 1 VU, 1 minute - verify API works
2. Average load: 10 VUs, 5 minutes - normal traffic
3. Stress test: ramp to 100 VUs over 10 minutes - find breaking point
4. Spike test: 0 -> 200 VUs in 10 seconds - sudden traffic spike

For each endpoint:
- Include realistic think time between requests
- Use CSV SharedArray for test data
- Add response time thresholds:
  - p95 < 500ms
  - p99 < 1000ms
  - Error rate < 1%

Output: tests/performance/ (one k6 script per endpoint group)
```

---

## 10.8 Contract Test Generation

```
Generate Pact contract tests between our React frontend and Node.js backend.

Consumer (Frontend) perspective - #file:src/api/productApi.ts:
Generate consumer-side Pact tests that define:
1. What requests the frontend makes
2. What response shape it expects (not the full response, just what it uses)
3. Scenarios: success, not found, server error

Provider (Backend) perspective - #file:src/controllers/productController.ts:
Generate provider verification tests that:
1. Load contracts from pact broker
2. Set up provider state (seed database)
3. Verify all consumer expectations

Output: 
- tests/contracts/product.consumer.pact.spec.ts
- tests/contracts/product.provider.pact.spec.ts
```

---

## Knowledge Check

1. What's the difference between generating tests from source code vs. from a specification?
2. When would you use property-based tests vs. data-driven tests?
3. What is contract testing and when would you generate contract tests?

---

## Exercises

**Exercise 10.1:** Take any existing function in your project. Use `/tests` to generate tests. Then enhance with a follow-up prompt for edge cases.

**Exercise 10.2:** Find your team's OpenAPI spec (or use the sample in labs/lab-05). Generate a complete API test suite from it.

**Exercise 10.3:** Generate a TestDataFactory for a domain of your choice. Verify it produces valid, consistent fake data.

---

## Next Module

➡️ [Module 11: End-to-End Automation with Copilot](../module-11/README.md)
