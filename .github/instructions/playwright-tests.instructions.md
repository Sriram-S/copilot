---
applyTo: "tests/**/*.spec.ts"
---

# Playwright Test File Instructions

These instructions apply to ALL Playwright test files in the `tests/` directory.

## Imports
Always import from the custom fixtures file, never directly from @playwright/test:
```typescript
// ✅ Correct
import { test, expect } from '../fixtures';

// ❌ Wrong
import { test, expect } from '@playwright/test';
```

## File Structure

Every test file must follow this structure:
```typescript
import { test, expect } from '../fixtures';
import { TestDataFactory } from '../src/data/TestDataFactory';

const factory = new TestDataFactory();

test.describe('[Feature Name]', () => {
  test.beforeEach(async ({ page }) => {
    // Common setup only - navigation if all tests need it
  });

  test('user can [action] [context] @smoke @regression', async ({ page, pageObject }) => {
    // Arrange
    const testData = factory.createRelevantEntity();

    // Act
    await pageObject.performAction(testData);

    // Assert
    await expect(page.locator('.result')).toHaveText('Expected result');
  });
});
```

## Test Tags

Include at least one of these tags in every test name:
- `@smoke` — Critical path, runs on every PR (max 10% of tests)
- `@regression` — Full regression suite (most tests)
- `@negative` — Error/failure scenarios
- `@critical` — Business-critical paths with high risk
- `@a11y` — Accessibility tests
- `@visual` — Visual regression tests
- `@api` — API-level tests

Tags go at the END of the test name string:
```typescript
test('user can successfully checkout @smoke @regression', ...)
test('checkout fails with expired card @regression @negative', ...)
```

## Assertion Rules

1. Always use Playwright's built-in auto-waiting assertions (not manual waits):
```typescript
// ✅ Auto-waits
await expect(locator).toBeVisible();
await expect(locator).toHaveText('...');
await expect(locator).toHaveValue('...');

// ❌ Manual check (no auto-wait)
expect(await locator.isVisible()).toBe(true);
```

2. Be specific - prefer `toHaveText()` over `toContainText()` when exact match is known

3. Use `toBeHidden()` not `not.toBeVisible()` (more readable)

## Forbidden in Test Files

- ❌ `page.waitForTimeout()` — causes flakiness and is banned
- ❌ Hardcoded credentials: passwords, tokens, email addresses
- ❌ Hardcoded IDs: user IDs, product IDs, order IDs
- ❌ `page.locator('.css-class')` in test files — selectors belong in Page Objects only
- ❌ `try/catch` blocks — let test failures propagate
- ❌ `test.only` — must be removed before committing
- ❌ `test.skip` without a comment with a ticket number: `test.skip('TODO: ABC-123')`
