# Lab 04: Agent Mode - Full Project Scaffold

## Objective
Experience the full power of Copilot Agent mode by scaffolding a complete Playwright test automation framework in a single agent session.

## Duration
60 minutes

## Prerequisites
- VS Code 1.99+ with GitHub Copilot Chat extension
- Agent mode enabled: `"chat.agent.enabled": true` in settings
- Node.js 18+

---

## Pre-Lab: Enable Agent Mode

1. Open VS Code Settings (`Ctrl+,`)
2. Search for "chat.agent"
3. Enable `Chat: Agent Enabled`
4. Open Copilot Chat panel (`Ctrl+Alt+I`)
5. Click the mode toggle (≡) and select "Agent"

---

## Exercise 1: The Grand Scaffold Prompt

Create an empty directory and open it in VS Code:

```bash
mkdir copilot-agent-lab && cd copilot-agent-lab
code .
```

Switch to Agent mode in Copilot Chat. Then paste this prompt:

```
You are an expert test automation architect.

I need you to scaffold a complete Playwright TypeScript test automation 
framework for testing the SauceDemo application (https://www.saucedemo.com/).

SCAFFOLD THESE FILES:

1. package.json with dependencies:
   - @playwright/test (latest)
   - @faker-js/faker
   - allure-playwright
   - dotenv

2. playwright.config.ts with:
   - baseURL from environment variable
   - 4 parallel workers in CI, 2 locally
   - Retry: 2 in CI, 0 locally
   - Reporters: html, allure-playwright
   - Screenshots: on failure
   - Videos: on failure
   - Trace: on first retry

3. .env.example with:
   - BASE_URL
   - STANDARD_USER credentials
   - ADMIN_USER credentials

4. src/pages/BasePage.ts - abstract base class
5. src/pages/LoginPage.ts - login page POM
6. src/pages/InventoryPage.ts - product listing POM
7. src/pages/CartPage.ts - cart POM
8. src/pages/CheckoutPage.ts - checkout flow POM (2 steps)
9. src/pages/OrderConfirmationPage.ts - confirmation POM

10. src/fixtures/index.ts - custom fixtures that:
    - Provide page objects as fixtures
    - Provide an authenticatedPage fixture that logs in before the test

11. src/data/TestDataFactory.ts - test data factory with:
    - Customer data generation with faker
    - Checkout info (firstName, lastName, zipCode)

12. tests/auth.spec.ts - authentication tests
13. tests/checkout.spec.ts - full checkout flow tests
14. tests/inventory.spec.ts - product listing tests

15. .github/workflows/playwright.yml - GitHub Actions CI

AFTER CREATING ALL FILES:
1. Run: npm install
2. Run: npx playwright install chromium
3. Copy .env.example to .env and set BASE_URL=https://www.saucedemo.com
4. Run: npx playwright test --grep @smoke --reporter=list
5. Show me the test results
6. Fix any failures

Begin now.
```

---

## Exercise 2: Observe and Interact with the Agent

As the agent works, observe:

1. **Tool calls** – See every file the agent creates, every command it runs
2. **Approval gates** – Review each file diff before accepting
3. **Self-correction** – Watch the agent fix issues it encounters
4. **Terminal output** – See real test results

**Interact if needed:**
- If agent asks a question, answer it
- If a step fails, let the agent try to fix it (give it 2-3 attempts)
- If stuck, guide with: "Try running just the smoke tests first"

---

## Exercise 3: Extend with an Agent Follow-Up

After the initial scaffold is complete:

```
The scaffold looks good. Now extend it:

1. Add a custom ESLint config (.eslintrc.js) that enforces:
   - No page.waitForTimeout()
   - All tests must have a describe block
   - No hardcoded selectors (prefer getByRole/getByTestId)

2. Add a Playwright visual regression test:
   tests/visual/login.visual.spec.ts
   Tests the login page in default, error, and success states

3. Add an accessibility test:
   tests/a11y/login.a11y.spec.ts
   Uses @axe-core/playwright to check for violations

4. Update playwright.config.ts to include these new test paths

5. Run all tests and report results
```

---

## Exercise 4: The Self-Healing Agent

Intentionally introduce a bug:

1. Open `src/pages/LoginPage.ts`
2. Change one locator to an invalid value: 
   ```typescript
   // Change this:
   readonly usernameInput = this.page.getByTestId('username');
   // To this:
   readonly usernameInput = this.page.getByTestId('username-BROKEN');
   ```

3. Run the tests to confirm they fail

4. Tell the agent:
   ```
   The login tests are now failing. Please:
   1. Run the failing tests
   2. Analyze the error output
   3. Find the root cause in the source files
   4. Fix the issue
   5. Re-run to confirm they pass
   ```

Observe the agent debugging and self-healing process.

---

## Reflection Questions

1. How many files did the agent create and modify autonomously?
2. How many times did you need to intervene?
3. What was the quality of the generated code? What would you change?
4. How long would this have taken without Copilot?

---

## Expected Outcome

- [ ] 15+ files created by the agent
- [ ] `npm install` completed successfully
- [ ] Smoke tests passing
- [ ] Visual and accessibility tests added
- [ ] Self-healing exercise completed

---

## Agent Session Tips

- **Don't rush to intervene** – Let the agent try to fix issues before stepping in
- **Use follow-up prompts** – "Make the tests more data-driven" is a valid follow-up
- **Review every file** – The agent can make mistakes, especially in complex logic
- **Save the agent transcript** – Take notes on what worked and what didn't

---

## Next Lab

➡️ [Lab 05: Custom Agents & Prompt Files](../lab-05/README.md)
