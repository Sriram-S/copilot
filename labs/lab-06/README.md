# Lab 06: Custom Instructions & Workspace Configuration

## Objective
Master the `.github/copilot-instructions.md` system to ensure all Copilot output follows your team's exact conventions without repeating yourself in every prompt.

## Duration
30 minutes

## Prerequisites
- Any existing test project (from previous labs or your own)
- VS Code with GitHub Copilot

---

## The Problem This Solves

Without instructions, Copilot generates generic code that may:
- Use the wrong base class
- Use deprecated selectors
- Hardcode test data
- Use wrong assertion library
- Not follow your naming conventions

With instructions, every single generation automatically follows your standards.

---

## Exercise 1: Measure the Before State

Before adding any instructions, generate a test using this simple prompt:

```
Generate a Playwright test for logging into SauceDemo with the standard user.
```

**Save the output.** Note any issues:
- Does it extend the right base class?
- Does it use the right locator strategy?
- Does it hardcode credentials?
- Does it follow your naming conventions?

---

## Exercise 2: Create Comprehensive Workspace Instructions

Create `.github/copilot-instructions.md`:

```markdown
# Copilot Workspace Instructions

## Project Overview
This is a Playwright TypeScript test automation framework for SauceDemo
(https://www.saucedemo.com/).

## Technology Stack
| Tool | Version | Purpose |
|------|---------|---------|
| Playwright | 1.44+ | E2E testing framework |
| TypeScript | 5.x strict | Language |
| Node.js | 20.x | Runtime |
| Allure | 2.x | Test reporting |
| Faker.js | 8.x | Test data generation |

## Architecture

### Imports
- Tests: `import { test, expect } from '../fixtures';` (NOT from @playwright/test directly)
- Page Objects: `import { Page } from '@playwright/test';`

### Page Object Rules
- Location: `src/pages/[Name]Page.ts`
- Must extend `BasePage` from `src/pages/BasePage.ts`
- Locator priority: getByRole > getByLabel > getByTestId > getByText > CSS (last resort)
- NO waitForTimeout() anywhere
- All methods: async, return Promise<void> or specific type
- JSDoc required on all public methods

### Test Rules
- Location: `tests/[feature].spec.ts`
- Import: `import { test, expect } from '../fixtures';`
- Use fixtures for page objects: `async ({ loginPage, inventoryPage }) =>`
- Test names: readable sentences describing the scenario
- Mandatory test tags: @smoke (critical path), @regression (all tests), @negative (error cases)
- Structure: Arrange-Act-Assert with blank lines between sections
- NO hardcoded credentials or test data - use TestDataFactory or env variables

### Test Data
- Factory: `src/data/TestDataFactory.ts`
- Credentials: `process.env.STANDARD_USER` and `process.env.STANDARD_PASSWORD`
- NEVER hardcode: 'secret_sauce', 'standard_user', email addresses

### Assertions
- Use Playwright expect() ONLY
- Prefer auto-waiting assertions: toBeVisible(), toContainText(), toHaveValue()
- Never: expect(await locator.isVisible()).toBe(true) - use expect(locator).toBeVisible()

## Naming Conventions
- Files: camelCase for classes, kebab-case for specs (login-flow.spec.ts)
- Test methods: sentence case ('should show error when password is missing')
- Locators: camelCase noun describing the element (submitButton, emailInput)
- Page Objects: PascalCase + Page suffix (LoginPage, CheckoutPage)

## Forbidden Patterns
- ❌ page.waitForTimeout() - causes flakiness
- ❌ Hardcoded test data in tests
- ❌ Direct @playwright/test import in test files
- ❌ Thread.sleep() equivalent (setTimeout in await)
- ❌ try/catch in tests (let failures propagate naturally)
- ❌ .nth(0) selectors without explanation

## CI/CD Context
- GitHub Actions, ubuntu-latest
- 4 parallel shards for regression suite
- ENV variables via GitHub Secrets (not in code)
- Artifacts: playwright-report/ uploaded on every run
```

---

## Exercise 3: Measure the After State

Now generate the same test again:

```
Generate a Playwright test for logging into SauceDemo with the standard user.
```

**Compare the output** to what you saved in Exercise 1.

Document the differences:
- [ ] Now extends BasePage/uses correct import?
- [ ] Uses correct locator strategy?
- [ ] Uses env variable instead of hardcoded credentials?
- [ ] Has correct test tags?
- [ ] Has correct import path?

---

## Exercise 4: Add Framework-Specific Scoped Instructions

Create `.github/instructions/playwright-tests.instructions.md`:

```markdown
---
applyTo: "tests/**/*.spec.ts"
---

# Playwright Test File Instructions

Every test file must:
1. Start with `import { test, expect } from '../fixtures';`
2. Have a `describe` block named after the feature being tested
3. Use `test.beforeEach` for common navigation, not `test.beforeAll`
4. Have at least one @smoke tagged test (the most critical scenario)

Test tags go at the END of the test name:
```typescript
test('user can log in with valid credentials @smoke @regression', ...)
test('user sees error with invalid password @regression @negative', ...)
```

Never mix UI and API calls in the same test. If you need API setup, 
use `request` fixture in beforeEach.
```

Create `.github/instructions/page-objects.instructions.md`:

```markdown
---
applyTo: "src/pages/**/*.ts"
---

# Page Object Instructions

Every Page Object:
1. Starts with `import { Page } from '@playwright/test';`
2. Second import is `import { BasePage } from './BasePage';`
3. Exports the class as a named export (not default)
4. Private locators are defined as `readonly` properties
5. The constructor signature is ALWAYS: `constructor(readonly page: Page)`

Locator naming pattern: [element descriptor][Element type]
Examples: submitButton, emailInput, errorMessage, productCard

Method naming pattern: [verb][Object]
Examples: login(), fillEmail(), selectProduct(), assertCartCount()
```

---

## Exercise 5: Test Instruction Effectiveness

Generate five different types of code and verify instructions are followed:

1. A new Page Object
2. A test file  
3. An API helper
4. A test data factory entry
5. A fixture

For each, note any deviations from your instructions.

---

## Exercise 6: Team Instructions Template

Create a template that other projects in your organization can adopt:

```markdown
# Copy this to .github/copilot-instructions.md in your project
# Fill in the sections marked with [FILL IN]

# Copilot Workspace Instructions

## Project Overview
[FILL IN: Project name, what it tests, URL]

## Technology Stack
[FILL IN: Framework, language, test runner, assertion library, reporter]

## Architecture
### Base Classes
[FILL IN: What base classes exist and when to use them]

### File Locations
[FILL IN: Where to put page objects, tests, helpers, fixtures]

### Import Rules
[FILL IN: What to import from where]

## Standards
### Locator Priority
[FILL IN: Your preferred locator hierarchy]

### Assertion Library
[FILL IN: Which assertion library and style to use]

### Test Data Strategy
[FILL IN: How test data is managed]

## Forbidden Patterns
[FILL IN: Anti-patterns specific to your project]

## CI/CD Context
[FILL IN: CI platform, environment names, how tests run]
```

---

## Reflection Questions

1. Which instruction had the most impact on output quality?
2. Are there any instructions that Copilot consistently ignores? Why might that be?
3. How would you onboard a new team member using your instructions file?

---

## Expected Outcome

- [ ] `.github/copilot-instructions.md` created with comprehensive instructions
- [ ] `.github/instructions/playwright-tests.instructions.md` created
- [ ] `.github/instructions/page-objects.instructions.md` created
- [ ] Before/after comparison documented
- [ ] Template file created for team use

---

## Next Lab

➡️ [Lab 07: Debugging & Flaky Test Analysis](../lab-07/README.md)
