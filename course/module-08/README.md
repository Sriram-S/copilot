# Module 08: Custom Instructions for Test Automation

## Learning Objectives

- Understand all three types of custom instruction files
- Write effective `copilot-instructions.md` for automation projects
- Create scoped `.instructions.md` files for different frameworks
- Use instructions to enforce coding standards without repetition

---

## 8.1 The Three Instruction Mechanisms

| Mechanism | File | Scope | Purpose |
|---|---|---|---|
| **Workspace Instructions** | `.github/copilot-instructions.md` | All chat in workspace | Global context, team standards |
| **Instruction Files** | `.github/instructions/*.instructions.md` | Scoped by glob pattern | Framework-specific rules |
| **Chat Settings** | VS Code settings `github.copilot.chat.*.instructions` | All chat globally | User-level preferences |

---

## 8.2 The Global Workspace File

`.github/copilot-instructions.md` is automatically included in every Copilot interaction in your workspace. This is the most powerful configuration tool available.

### What to Include

1. **Technology stack** – Exact versions, frameworks, libraries
2. **Architectural patterns** – POM, fixture patterns, base classes
3. **Naming conventions** – File names, method names, test IDs
4. **Quality standards** – Coverage requirements, assertion libraries
5. **Forbidden patterns** – Anti-patterns to explicitly avoid
6. **Test data strategy** – How to handle test data (factory, fixtures, seeded DB)

### Example for a Playwright TypeScript Project

```markdown
# Copilot Workspace Instructions

## Project Overview
This is an enterprise test automation framework for the AcmeCorp e-commerce platform.

## Technology Stack
- **Framework**: Playwright 1.44+ with TypeScript
- **Test Runner**: Playwright Test (@playwright/test)
- **Language**: TypeScript 5.x strict mode
- **Assertions**: Playwright's built-in expect() only - never use Jest expect or Chai
- **Reporting**: Allure Reporter + Playwright HTML report
- **CI**: GitHub Actions

## Architecture
### Page Object Model
- All page classes live in `src/pages/`
- All page classes MUST extend `BasePage` from `src/pages/BasePage.ts`
- Use Playwright's built-in Locator API, never use string selectors directly
- Prefer getByRole(), getByText(), getByLabel() over CSS/XPath
- Never use page.waitForTimeout() - use proper await + locator assertions

### Test Files
- All test files: `tests/**/*.spec.ts`
- Use descriptive test names that read like requirements
- Every test must have a @tag annotation (e.g., @smoke, @regression, @critical)
- Tests must be independent - no shared state between tests

### Fixtures
- Custom fixtures are in `src/fixtures/`
- Always use the custom `test` import from `src/fixtures/index.ts` not @playwright/test directly
- Authentication state is managed by the `authenticatedPage` fixture

### Test Data
- NEVER hardcode test data (emails, passwords, product IDs) in test files
- Use `TestDataFactory` from `src/data/TestDataFactory.ts`
- API-generated test data preferred over UI-generated for performance

## Code Standards
- Use async/await everywhere - never .then()
- Destructure fixtures in test arguments: `test('name', async ({ page, loginPage }) =>`
- One assertion concept per test (but multiple expect() calls are fine)
- All helper methods in Page Objects must have JSDoc

## Forbidden Patterns
- ❌ page.waitForTimeout() - use locator assertions
- ❌ Hardcoded URLs - use environment configuration
- ❌ Thread.sleep() / setTimeout() without reason
- ❌ try/catch in tests - let failures propagate
- ❌ Test data cleanup in afterEach - use API teardown in global teardown
```

---

## 8.3 Scoped Instruction Files

`.github/instructions/*.instructions.md` files are applied based on `applyTo` glob patterns.

### File Format

```markdown
---
applyTo: "tests/api/**/*.spec.ts"
---

# API Test Instructions

All API tests in this folder use Supertest with Jest.

- Use the `apiClient` helper from `src/helpers/apiClient.ts`
- Every test must validate: status code, response schema, response time
- Use `expect(response).toMatchSchema(schema)` from the json-schema-matcher helper
- Group tests by HTTP method within a describe block
- Always include Authorization header tests (with and without token)
```

### Examples for Different Frameworks

**Playwright instruction file:**
```markdown
---
applyTo: "tests/e2e/**"
---
Use Playwright's `expect(locator).toBeVisible()` for visibility checks,
never check `.isVisible()` in an if statement.
Always use `page.goto()` with `{ waitUntil: 'networkidle' }` for SPAs.
```

**Java/Selenium instruction file:**
```markdown
---
applyTo: "src/test/java/**/*.java"
---
All Selenium tests extend `BaseTest.java`.
Use WebDriverWait with `ExpectedConditions`, never `Thread.sleep()`.
Page Factory: use `@FindBy` annotations, initialize with `PageFactory.initElements()`.
Test annotations: @Test(groups = {"smoke"}) or @Test(groups = {"regression"}).
```

---

## 8.4 User-Level Instructions via Settings

```json
// settings.json
{
  "github.copilot.chat.codeGeneration.instructions": [
    {
      "text": "When generating tests, always include both happy path and sad path scenarios"
    },
    {
      "text": "Prefer data-driven tests with parametrize/DataProvider over duplicated test methods"
    },
    {
      "file": ".github/instructions/my-preferences.instructions.md"
    }
  ],
  "github.copilot.chat.testGeneration.instructions": [
    {
      "text": "Generated tests must follow the Arrange-Act-Assert (AAA) pattern with clear comments"
    }
  ]
}
```

---

## 8.5 Instructions for Common Automation Frameworks

### Cypress Instructions

```markdown
---
applyTo: "cypress/**/*.cy.js"
---

## Cypress-Specific Rules
- Use `cy.intercept()` for all API mocking, never use fixtures directly in tests
- Custom commands are in `cypress/support/commands.js` - always check before creating new ones
- Use `data-testid` attributes for selectors, never CSS classes or IDs
- Each spec file should have a `before()` that seeds required test data via API
- Use `cy.session()` for authentication to avoid logging in before every test
- Aliases: `cy.get('@alias')` preferred over storing in variables
```

### K6 Performance Test Instructions

```markdown
---
applyTo: "tests/performance/**/*.js"
---

## K6 Performance Test Rules
- Always define thresholds for: http_req_duration p95, http_req_failed
- Use `check()` not `expect()` (wrong library)
- Parameterize load profiles: smoke (1 VU), average (10 VU), stress (50 VU), spike (100 VU)
- Read test data from CSV files using SharedArray for memory efficiency
- Group related requests with `group()` for clarity in reports
```

---

## 8.6 Instructions for CI/CD Integration

```markdown
# .github/copilot-instructions.md addition

## CI/CD Context
Our GitHub Actions pipeline:
- Runs on: ubuntu-latest
- Node version: 20.x
- Test sharding: 4 shards for E2E tests
- Artifacts: Upload Playwright report, screenshots, videos on failure
- Environments: dev, staging, production (via environment secrets)

When generating GitHub Actions workflows:
- Use `ubuntu-latest`
- Cache node_modules and playwright browsers
- Use `--shard` flag for parallel E2E execution
- Always upload test artifacts on failure: `if: failure()`
```

---

## 8.7 Measuring Instruction Effectiveness

Test your instructions by asking Copilot to generate the same code before and after adding instructions:

1. **Before**: Generate a Playwright test without instructions. Note any anti-patterns.
2. **Add instructions** to `.github/copilot-instructions.md`
3. **After**: Generate the same test. Verify Copilot now follows your conventions.

Common signs of ineffective instructions:
- Copilot still uses `page.waitForTimeout()`
- Test data is still hardcoded
- Wrong base class is extended

Fix by making instructions more explicit and providing examples.

---

## Knowledge Check

1. What is the difference between `.github/copilot-instructions.md` and a `.instructions.md` file?
2. How do you apply an instruction file only to files in `tests/api/`?
3. Why is it important to include "forbidden patterns" in your instructions?

---

## Exercises

**Exercise 8.1:** Write a `copilot-instructions.md` for a Selenium Java project. Include: framework versions, base class requirements, forbidden patterns, and naming conventions.

**Exercise 8.2:** Create an `.instructions.md` file scoped to `**/*.spec.ts` files. Include three specific rules about Playwright test structure.

**Exercise 8.3:** Test the effectiveness of your instructions: generate a test before and after adding the instructions. Document the differences.

---

## Next Module

➡️ [Module 09: Prompt Engineering for Testers](../module-09/README.md)
