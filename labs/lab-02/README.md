# Lab 02: Page Object Model Generation with Copilot

## Objective
Generate a complete Page Object Model (POM) framework for a demo e-commerce site using Copilot.

## Duration
45 minutes

## Prerequisites
- VS Code with GitHub Copilot installed
- Node.js 18+

## Setup

```bash
mkdir copilot-lab-02 && cd copilot-lab-02
npm init playwright@latest
# Select: TypeScript, tests folder: tests, add GitHub Actions: No
```

---

## Exercise 1: Generate BasePage

Create `src/pages/BasePage.ts` and type this comment block, then let Copilot complete:

```typescript
/**
 * Base page class that all Page Objects extend.
 * Provides common navigation, waiting, and interaction utilities.
 * Uses Playwright's Page object.
 */
```

**Expected Copilot completion includes:**
- Constructor taking `Page`
- `navigate(path: string)` method
- `waitForLoadState()` method
- `getTitle()` method
- `screenshot()` method

---

## Exercise 2: Generate LoginPage via Chat

Open Copilot Chat and enter:

```
Generate a Playwright TypeScript Page Object class for the login page at:
https://www.saucedemo.com/

The page has:
- Username input (data-test="username")
- Password input (data-test="password")
- Login button (data-test="login-btn")
- Error message container (data-test="error")

Requirements:
- Extend BasePage from src/pages/BasePage.ts
- Methods: login(username, password), getErrorMessage(), isLoggedIn()
- Use getByTestId() locators
- Include JSDoc for each method

Output: src/pages/LoginPage.ts
```

---

## Exercise 3: Generate InventoryPage via Inline Completion

After generating `LoginPage.ts`, create `src/pages/InventoryPage.ts`:

Type the class header and Copilot will pattern-match from the LoginPage:

```typescript
import { Page } from '@playwright/test';
import { BasePage } from './BasePage';

/**
 * Page Object for the SauceDemo inventory/products page
 * URL: https://www.saucedemo.com/inventory.html
 */
export class InventoryPage extends BasePage {
```

**Let Copilot generate:** It should create locators and methods for:
- Product names
- Add to cart buttons
- Sorting dropdown
- Cart badge

---

## Exercise 4: Generate Tests Using the Page Objects

In Copilot Chat:

```
Using #file:src/pages/LoginPage.ts and #file:src/pages/InventoryPage.ts,
generate a comprehensive Playwright test suite.

Test credentials:
- Valid: username="standard_user", password="secret_sauce"
- Locked: username="locked_out_user", password="secret_sauce"
- Invalid: any random username/password

Generate tests for:
1. Successful login - lands on inventory page
2. Login with locked user - shows error message
3. Login with invalid credentials - shows error message
4. Login with empty username - shows error message
5. Login with empty password - shows error message

Output: tests/auth.spec.ts
```

---

## Exercise 5: Run and Debug

```bash
npx playwright test tests/auth.spec.ts --reporter=list
```

If any tests fail, use Copilot Chat:

```
@terminal The tests are failing. What is the root cause and how do I fix it?
```

---

## Reflection Questions

1. How many lines of Page Object code did Copilot generate vs. how many you wrote?
2. What naming conventions did Copilot follow? Are they consistent with your team's standards?
3. What would you add to `.github/copilot-instructions.md` to improve the output?

---

## Expected Outcome

- [ ] `src/pages/BasePage.ts` created
- [ ] `src/pages/LoginPage.ts` created  
- [ ] `src/pages/InventoryPage.ts` created
- [ ] `tests/auth.spec.ts` created with 5+ tests
- [ ] All tests pass against https://www.saucedemo.com/

---

## Bonus Challenge

Generate `CartPage.ts` and `CheckoutPage.ts` using the same approach. Write tests for adding items to cart and completing the checkout flow.

---

## Next Lab

➡️ [Lab 03: API Test Generation](../lab-03/README.md)
