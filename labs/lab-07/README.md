# Lab 07: Debugging & Flaky Test Analysis with Copilot

## Objective
Use Copilot Chat and agent mode to diagnose and fix common test automation problems: flaky tests, broken selectors, timing issues, and CI-specific failures.

## Duration
60 minutes

## Prerequisites
- Completion of Lab 04 (or an existing Playwright project with some tests)
- VS Code with Copilot

---

## Part A: Diagnosing Flaky Tests

### Exercise A1: Introduce a Flaky Test

Take a passing test and make it flaky. Add this test to your test suite:

```typescript
// tests/flaky-examples.spec.ts
import { test, expect } from '@playwright/test';

test('flaky: cart badge updates immediately after add @flaky-example', async ({ page }) => {
  await page.goto('https://www.saucedemo.com/');
  await page.fill('[data-test="username"]', 'standard_user');
  await page.fill('[data-test="password"]', 'secret_sauce');
  await page.click('[data-test="login-btn"]');
  
  // DELIBERATELY FLAKY: No proper wait for page load
  await page.click('[data-test="add-to-cart-sauce-labs-backpack"]');
  
  // This might pass or fail depending on timing
  const badge = page.locator('.shopping_cart_badge');
  expect(await badge.textContent()).toBe('1');  // Missing await on assertion!
});

test('flaky: search results appear after typing @flaky-example', async ({ page }) => {
  await page.goto('https://www.saucedemo.com/inventory.html');
  
  // DELIBERATELY FLAKY: Race condition between sort and assertion
  await page.selectOption('[data-test="product_sort_container"]', 'za');
  
  // Get first product name - flaky because sort might not be complete
  const firstName = await page.locator('.inventory_item_name').first().textContent();
  expect(firstName).toBe('Test.allTheThings() T-Shirt (Red)');
});
```

### Exercise A2: Run and Capture the Failures

```bash
npx playwright test tests/flaky-examples.spec.ts --reporter=list --repeat-each=5
```

Copy the failure output.

### Exercise A3: Ask Copilot to Diagnose

In Copilot Chat:

```
I have two flaky Playwright tests. Here is the test code:

[paste the test code]

Here is the failure output from 5 runs:

[paste the failure output]

Please:
1. Identify exactly why each test is flaky
2. Explain the race condition or timing issue
3. Show the fixed version of each test
4. Add a comment explaining the fix for future maintainers
```

### Exercise A4: Apply and Verify the Fix

Apply Copilot's fixes. Run with `--repeat-each=10` to verify stability.

---

## Part B: Debugging Broken Selectors

### Exercise B1: Create Tests with Fragile Selectors

```typescript
// tests/fragile-selectors.spec.ts
test('login with fragile selectors', async ({ page }) => {
  await page.goto('https://www.saucedemo.com/');
  
  // FRAGILE: CSS class might change
  await page.click('.btn_action.btn_signin_credentials');
  
  // FRAGILE: nth-child is order-dependent
  await page.fill('form input:nth-child(1)', 'standard_user');
  await page.fill('form input:nth-child(2)', 'secret_sauce');
  
  // FRAGILE: XPath with text that might change
  await page.click('//button[contains(@class, "submit-button")]');
});
```

### Exercise B2: Ask Copilot to Improve Selectors

Select the test code and use Copilot Chat:

```
These selectors in my test are fragile and will break when the app changes.
#selection

For each selector:
1. Explain why it's fragile
2. Provide a more resilient alternative using Playwright's semantic selectors
3. Explain why the new selector is better

Provide the complete refactored test.
```

---

## Part C: CI vs. Local Failures

### Exercise C1: Understand CI Differences

Create a prompt for a common CI debugging scenario:

```
Our Playwright tests pass locally but fail in GitHub Actions CI.

Local environment: macOS, Chrome, headed mode
CI environment: ubuntu-latest, Chromium, headless mode

Common failure in CI:
TimeoutError: page.waitForSelector: Timeout 30000ms exceeded
  Call log:
  - waiting for locator('[data-test="checkout_info"]') to be visible

The test:
1. Logs in successfully
2. Adds item to cart
3. Clicks checkout
4. Then times out waiting for the checkout info page

Questions:
1. What causes this type of failure specifically in headless Linux CI?
2. What are the 5 most common reasons tests fail in CI but pass locally?
3. Show me the Playwright configuration changes to debug CI failures
4. What additional tracing/logging should I add?
```

### Exercise C2: Add CI-Specific Debugging

In Copilot Chat:

```
Update playwright.config.ts to add better CI debugging:

1. Enable trace on first retry (already done) but also on first run in CI
2. Add a custom reporter that logs:
   - Test name
   - Test duration
   - Each step with timestamp
3. Add a global setup that:
   - Verifies the base URL is reachable before running tests
   - Logs the browser version being used
   - Logs the CI environment variables

Show me the complete updated playwright.config.ts
```

---

## Part D: Analyzing a Real Failure

### Exercise D1: Simulate a Complex Failure

Introduce a real failure scenario:

```typescript
test('checkout fails due to stale element', async ({ page }) => {
  await page.goto('https://www.saucedemo.com/');
  await page.fill('[data-test="username"]', 'standard_user');
  await page.fill('[data-test="password"]', 'secret_sauce');
  await page.click('[data-test="login-btn"]');
  
  // Wait for inventory, get product reference
  const firstProduct = page.locator('.inventory_item').first();
  
  // Navigate away and back (simulates SPA navigation)
  await page.goto('https://www.saucedemo.com/cart.html');
  await page.goto('https://www.saucedemo.com/inventory.html');
  
  // This reference is now stale after navigation
  const name = await firstProduct.textContent(); // Stale element!
  expect(name).toBeTruthy();
});
```

### Exercise D2: Visual Debugging with Playwright Inspector

```bash
PWDEBUG=1 npx playwright test tests/flaky-examples.spec.ts --headed
```

Then ask Copilot:

```
I used PWDEBUG=1 to debug my test and observed:
- The element is briefly present, then the page re-renders
- By the time my locator tries to interact, the element reference is stale

How should I handle stale element references in Playwright?
Show me 3 different approaches:
1. Using locator auto-retry
2. Using page.waitForLoadState()
3. Using MutationObserver-based approach

Which approach is best for our SauceDemo scenario?
```

---

## Part E: Building a Debugging Toolkit

### Exercise E1: Create a Debug Helper

```
Generate a TypeScript debugging utility class for Playwright tests.

Include methods:
1. logNetworkRequests(page, filter?) - logs all XHR/fetch requests
2. captureConsoleLogs(page) - captures and returns all console messages
3. highlightElement(locator) - visually highlights an element for debugging
4. waitAndDebug(locator, message) - waits for locator and logs detailed info
5. dumpDOMState(page, selector) - dumps DOM content of a section for debugging
6. measurePageLoadTime(page, url) - measures and logs complete page load time

Output: src/helpers/DebugHelper.ts

Also generate a test that demonstrates each method.
```

---

## Reflection Questions

1. What was the most common root cause of flakiness in these exercises?
2. How does Playwright's Locator API help prevent flaky tests compared to older approaches?
3. What information do you need to give Copilot to get the most accurate debugging help?

---

## Expected Outcome

- [ ] Flaky tests identified and fixed with Copilot's help
- [ ] Fragile selectors replaced with semantic alternatives
- [ ] CI debugging configuration added to `playwright.config.ts`
- [ ] Stale element handling understood and applied
- [ ] `src/helpers/DebugHelper.ts` created

---

## Next Lab

➡️ [Lab 08: Full E2E Project](../lab-08/README.md)
