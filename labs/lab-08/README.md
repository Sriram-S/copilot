# Lab 08: Full End-to-End Project — New Feature Test Coverage

## Objective
Simulate a real-world workflow: receive a new feature requirement and use Copilot to go from zero to a fully tested, CI-ready feature branch with complete test coverage.

## Duration
90 minutes

## Prerequisites
- VS Code with Copilot (agent mode enabled)
- Existing Playwright project (from Lab 04) or fresh setup

---

## The Scenario

You are a QA Engineer on an e-commerce team. The development team has just merged a **"Product Wishlist"** feature. Your job is to:

1. Analyze the feature requirements
2. Build a test strategy
3. Implement all tests with Copilot assistance
4. Set up CI
5. Document the testing

---

## Feature Requirements

```
Feature: Product Wishlist

As a logged-in user, I want to save products to a wishlist
so that I can purchase them later.

Acceptance Criteria:

AC-1: Add to Wishlist from Product Listing
  - A heart icon appears next to each product on the inventory page
  - Clicking the heart icon adds the product to my wishlist
  - The heart icon becomes filled/red when product is in wishlist
  - Clicking filled heart removes the product from wishlist
  - A wishlist counter shows the number of items (appears in header)

AC-2: Wishlist Page
  - Accessing /wishlist shows all saved products
  - Each product shows: image, name, price, "Add to Cart" button, "Remove" button
  - Empty wishlist shows a friendly empty state message

AC-3: Wishlist Persistence
  - Wishlist persists after page refresh
  - Wishlist is user-specific (user A's wishlist ≠ user B's wishlist)
  - Wishlist is empty for a new user

AC-4: Wishlist to Cart
  - "Add to Cart" from wishlist adds item to cart
  - Item remains in wishlist after adding to cart (unless user removes it)
  - If item is already in cart, button shows "Already in Cart"

AC-5: Edge Cases
  - Cannot add the same product to wishlist twice
  - Maximum 50 items in wishlist (shows error at 51st)
  - Wishlist survives browser tab closure and re-open
  - Guest users see "Login to save to wishlist" tooltip
```

Note: We're simulating this feature. Use SauceDemo (https://www.saucedemo.com/) as the base app. Since SauceDemo doesn't have a real wishlist, you'll generate tests against INTENDED behavior — these would be run against a real implementation.

---

## Phase 1: Test Planning (15 min)

### Step 1: Generate a Test Plan

In Copilot Chat:

```
I'm a QA engineer who needs to test a new Wishlist feature for an e-commerce app.

Here are the acceptance criteria:

[Paste the acceptance criteria above]

Please generate a comprehensive test plan document.

Include:
1. Test objectives
2. In scope / Out of scope
3. Test scenarios table:
   | ID | Scenario | Type | Priority | Test Data Needed |
4. Test data requirements
5. Test environment requirements
6. Risk areas (what could go wrong)
7. Automation strategy (which tests to automate vs. manual)

Output: docs/test-plans/wishlist-test-plan.md
```

### Step 2: Review and Refine

Read the generated plan. Ask follow-up questions:

```
The test plan looks good. I have two concerns:
1. How do we test "wishlist persists after page refresh" without a real backend?
2. How do we test "user-specific wishlist" when our test env only has one user?

Suggest test strategies for both concerns.
```

---

## Phase 2: Test Infrastructure (20 min)

### Step 3: Create Page Objects

```
Based on the wishlist feature requirements, create these Page Objects:

1. src/pages/WishlistPage.ts
   - Navigate to /wishlist
   - Get count of wishlist items
   - Get list of product names in wishlist
   - Click "Add to Cart" for a product by name
   - Click "Remove" for a product by name
   - Check if wishlist is empty
   - Get the empty state message

2. Update src/pages/InventoryPage.ts to add:
   - clickWishlistHeart(productName: string) - add/remove from wishlist
   - isInWishlist(productName: string): Promise<boolean>
   - getWishlistCount(): Promise<number>

Use the same conventions as existing page objects.
If src/pages/InventoryPage.ts already exists, update it.
Otherwise create it.

Read #file:src/pages/BasePage.ts first for the base class structure.
```

### Step 4: Create Test Data

```
Add wishlist-related test data to the TestDataFactory.

Add methods:
- createWishlistItem(overrides?): { productName, productId }
- createWishlistItems(count: number): WishlistItem[]

Also add a wishlist state type:
- WishlistState: { items: WishlistItem[], maxCapacity: 50 }

Update: src/data/TestDataFactory.ts
```

### Step 5: Create Fixtures

```
Add wishlist fixtures to src/fixtures/index.ts:

- wishlistPage: WishlistPage - page object fixture
- emptyWishlist: fixture that clears wishlist via API/storage before test
- wishlistWithItems(count): fixture that pre-populates wishlist with N items
  (simulate by setting localStorage if no API is available)

Read #file:src/fixtures/index.ts first to understand existing fixture patterns.
```

---

## Phase 3: Test Generation (30 min)

### Step 6: Generate E2E Tests from Acceptance Criteria

```
Generate complete Playwright tests for the wishlist feature.

Use:
- #file:docs/test-plans/wishlist-test-plan.md for the scenario list
- #file:src/pages/WishlistPage.ts for the page object
- #file:src/pages/InventoryPage.ts for inventory interactions
- #file:src/fixtures/index.ts for fixtures
- #file:src/data/TestDataFactory.ts for test data

Generate:
- tests/e2e/wishlist/add-to-wishlist.spec.ts (AC-1 scenarios)
- tests/e2e/wishlist/wishlist-page.spec.ts (AC-2 scenarios)
- tests/e2e/wishlist/wishlist-persistence.spec.ts (AC-3 scenarios)
- tests/e2e/wishlist/wishlist-to-cart.spec.ts (AC-4 scenarios)
- tests/e2e/wishlist/wishlist-edge-cases.spec.ts (AC-5 scenarios)

Tag each test appropriately: @smoke, @regression, @negative, @wishlist
At least one @smoke test per file.
```

### Step 7: Generate API Tests

```
Generate API tests for the wishlist backend endpoints.

Assume these endpoints exist (they would in the real implementation):

GET /api/wishlist - returns user's wishlist
  Response: { items: [{ productId, productName, price, addedAt }], count: number }

POST /api/wishlist/items
  Request: { productId: string }
  Response 201: { item: WishlistItem }
  Response 409: { error: "ITEM_ALREADY_IN_WISHLIST" }
  Response 400: { error: "WISHLIST_FULL", message: "Max 50 items reached" }

DELETE /api/wishlist/items/:productId
  Response 200: { success: true }
  Response 404: { error: "ITEM_NOT_IN_WISHLIST" }

Auth: Bearer token in Authorization header

Generate: tests/api/wishlist.api.spec.ts
Use supertest + Jest + TypeScript
```

---

## Phase 4: CI/CD (15 min)

### Step 8: Generate CI Workflow

```
Generate a GitHub Actions workflow specifically for the wishlist feature tests.

Requirements:
- Trigger: pull_request where files changed match tests/e2e/wishlist/**
          OR any push to main
- Jobs:
  1. smoke-tests: Run @smoke tagged tests only (quick validation)
  2. wishlist-e2e: Run all tests/e2e/wishlist/*.spec.ts
  3. api-tests: Run tests/api/wishlist.api.spec.ts
  4. report: Collect results from all jobs and post PR comment

- Smoke tests must complete in under 5 minutes
- Upload Playwright report on all runs
- Upload failure screenshots on failure

Output: .github/workflows/wishlist-tests.yml
```

---

## Phase 5: Documentation (10 min)

### Step 9: Generate Test Documentation

```
@workspace Generate test documentation for the wishlist feature.

Create: docs/testing/wishlist-testing.md

Include:
1. Overview of what's tested
2. Test file structure (with descriptions of each spec file)
3. How to run wishlist tests only: npx playwright test --grep @wishlist
4. Test data: how wishlist test data is created
5. Known limitations (what's NOT tested and why)
6. How to debug wishlist test failures
7. Coverage summary: which ACs are covered by which tests

Make it suitable for both developers and manual QA reviewers.
```

---

## Phase 6: Validation

### Step 10: Run and Fix

```bash
# Run just the smoke tests first
npx playwright test --grep "@smoke and @wishlist" --reporter=list

# Then run the full wishlist suite
npx playwright test tests/e2e/wishlist/ --reporter=list
```

For any failures, use:

```
@terminal The wishlist tests are failing with this output. 
Please diagnose and fix.
```

---

## Retrospective

After completing the lab, answer these questions:

1. **Time estimation**: How long did this take? How long would it have taken without Copilot?
2. **Quality assessment**: Which generated tests needed the most manual review/fix?
3. **Coverage**: Are all 5 acceptance criteria covered by tests?
4. **What worked best**: Which Copilot feature helped most in this lab?
5. **What didn't work**: What required the most manual correction?

---

## Expected Outcome

- [ ] `docs/test-plans/wishlist-test-plan.md` created
- [ ] `src/pages/WishlistPage.ts` created
- [ ] `src/pages/InventoryPage.ts` updated
- [ ] 5 E2E test spec files created
- [ ] `tests/api/wishlist.api.spec.ts` created
- [ ] `.github/workflows/wishlist-tests.yml` created
- [ ] `docs/testing/wishlist-testing.md` created
- [ ] Smoke tests passing (or well-documented as requiring real app)

---

## Next Lab

➡️ [Lab 09: Capstone — IntelliJ + Java Selenium Suite](../lab-09/README.md)
