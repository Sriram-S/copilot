# Module 09: Prompt Engineering for Test Automation Engineers

## Learning Objectives

- Master the anatomy of an effective prompt for test generation
- Learn prompt patterns specific to automation: scaffolding, refactoring, analysis
- Write `.prompt.md` files for repeatable workflows
- Apply chain-of-thought and few-shot techniques to improve output quality

---

## 9.1 The Prompt Engineering Mindset

Prompting Copilot for test automation is like briefing a junior engineer:
- The more context you provide, the better the output
- Showing examples (few-shot) beats describing abstractly
- Breaking complex requests into steps yields better results
- Constraints reduce hallucination and enforce standards

---

## 9.2 The Anatomy of a High-Quality Prompt

```
[ROLE] You are an expert Playwright automation engineer...
[CONTEXT] We are testing an e-commerce checkout flow...
[TASK] Generate comprehensive tests for the CheckoutPage...
[CONSTRAINTS] Use our TestDataFactory, extend BaseTest, no hardcoded data...
[FORMAT] TypeScript, Playwright Test, Allure annotations...
[EXAMPLES] Follow the pattern in: #file:tests/cart.spec.ts
[OUTPUT] Create the file at: tests/checkout.spec.ts
```

---

## 9.3 Reusable Prompts with `.prompt.md` Files

`.prompt.md` files are stored in `.github/prompts/` and can be invoked from Chat using the `/` menu or by referencing them.

### File Structure

```markdown
---
mode: ask          # or 'edit' or 'agent'
description: Generate a Playwright Page Object for a given page
---

[Your prompt template here]
```

### Invoking a Prompt File

In VS Code Chat, click the **Attach** button (📎) and select "Prompt..." or type:
```
Use the prompt in #file:.github/prompts/generate-page-object.prompt.md
Apply it to generate a Page Object for the Settings page.
```

---

## 9.4 Core Prompt Patterns for Automation

### Pattern 1: Page Object Generation

```
You are a senior Playwright automation engineer.

Generate a TypeScript Page Object class for the [PAGE_NAME] page.

Page URL: [URL]
Key user actions on this page:
1. [Action 1]
2. [Action 2]
3. [Action 3]

Requirements:
- Class must extend BasePage from src/pages/BasePage.ts
- Use Playwright's Locator API (getByRole, getByLabel, getByTestId)
- Never use string selectors directly
- All public methods must be async and return Promise<void> or Promise<T>
- Include JSDoc for every public method
- Add a constructor that takes `page: Page`

Output file: src/pages/[PageName]Page.ts
```

### Pattern 2: API Test Generation

```
Generate RestAssured (Java) tests for the following endpoint:

Endpoint: [HTTP_METHOD] [URL_PATH]
Request body schema: [SCHEMA or "none"]
Response body schema: [SCHEMA]
Authentication: [Bearer token / Basic Auth / None]
Base class to extend: ApiBaseTest.java

Generate tests for:
1. Happy path - valid request returns expected status and body
2. Missing required fields - returns 400 with error details
3. Invalid field values - returns 422 with validation errors
4. Unauthorized request - returns 401
5. Forbidden request (wrong role) - returns 403
6. Resource not found - returns 404

Use AssertJ for assertions. Use TestDataBuilder for request bodies.
File: src/test/java/api/[ResourceName]ApiTest.java
```

### Pattern 3: Test Data Factory

```
Generate a TypeScript TestDataFactory class for [DOMAIN].

Entities needed:
- [Entity 1] with fields: [fields]
- [Entity 2] with fields: [fields]

Requirements:
- Use @faker-js/faker for realistic data generation
- Each entity has a `create[Entity](overrides?)` method
- Overrides object uses Partial<EntityType>
- All emails must be unique (use Date.now() or faker.unique)
- Include a `createMany[Entity](count, overrides?)` method
- Export as singleton: export const testDataFactory = new TestDataFactory()

Output: src/data/TestDataFactory.ts
```

### Pattern 4: Flaky Test Diagnosis

```
Analyze this test for potential flakiness:

[PASTE TEST CODE]

Check for:
1. Hard waits (waitForTimeout, Thread.sleep)
2. Race conditions between assertions and state changes
3. Selectors that depend on dynamic content (indices, generated IDs)
4. Missing network idle waits for SPA navigation
5. Test data that could conflict between parallel runs
6. Missing retry configuration for transient failures

For each issue found:
- Explain why it causes flakiness
- Provide the specific fix
- Show the before/after code
```

### Pattern 5: Coverage Gap Analysis

```
@workspace Analyze the test coverage for the [FEATURE_NAME] feature.

1. Find all source files related to [FEATURE_NAME] in src/
2. Find all existing test files that test [FEATURE_NAME] in tests/
3. Identify what is NOT tested:
   - Methods with no tests
   - Error paths that are not covered
   - Edge cases that are missing
4. Generate tests for the top 3 most critical gaps
5. Create a COVERAGE_GAPS.md documenting all findings
```

---

## 9.5 Few-Shot Prompting for Consistent Output

Providing examples dramatically improves consistency:

```
Generate a test for the product search feature.

Follow EXACTLY the same pattern as this existing test:

---EXAMPLE TEST---
test('user can add item to cart from search results', async ({ page, searchPage, cartPage }) => {
  // Arrange
  const query = testDataFactory.createSearchQuery({ category: 'electronics' });
  
  // Act
  await searchPage.search(query.term);
  await searchPage.addFirstResultToCart();
  
  // Assert  
  await expect(cartPage.itemCount).toContainText('1');
  await expect(cartPage.latestItem).toContainText(query.expectedProduct);
});
---END EXAMPLE---

Now generate:
test('user can filter search results by price range')
test('user can sort search results by rating')
test('search with no results shows empty state message')
```

---

## 9.6 Chain-of-Thought Prompts for Complex Scenarios

For complex test scenarios, ask Copilot to reason step-by-step:

```
Think step by step about how to test the "checkout as guest" user journey.

Step 1: List all the pages involved in the guest checkout flow
Step 2: For each page, list the actions the user takes
Step 3: For each action, identify what could go wrong
Step 4: Group the scenarios into: smoke (1 happy path), regression (critical paths), 
        edge cases (error conditions)
Step 5: Now generate the Playwright test file implementing all these scenarios
```

---

## 9.7 The `.prompt.md` Library for This Course

See `.github/prompts/` for the complete prompt library:

| File | Purpose |
|---|---|
| `generate-page-object.prompt.md` | Generate Playwright Page Objects |
| `generate-api-tests.prompt.md` | Generate API test suites |
| `generate-test-data.prompt.md` | Generate TestDataFactory entries |
| `analyze-flakiness.prompt.md` | Diagnose flaky tests |
| `coverage-gap-analysis.prompt.md` | Find coverage gaps |
| `generate-ci-workflow.prompt.md` | Generate GitHub Actions workflow |
| `review-test-quality.prompt.md` | Code review test quality |
| `generate-accessibility-tests.prompt.md` | Generate a11y test cases |

---

## 9.8 Prompt Anti-Patterns to Avoid

| Anti-Pattern | Problem | Fix |
|---|---|---|
| "Generate some tests" | Too vague | Specify page, actions, frameworks, patterns |
| "Fix this" (no context) | Missing context | Include the error, the code, and expected behavior |
| "Is this good?" | Yes/No questions | "Review this for coverage gaps and flakiness risks" |
| Asking for everything at once | Overwhelming context | Break into focused, sequential prompts |
| No examples | Inconsistent output | Always provide a reference test file |

---

## Knowledge Check

1. What are the six components of an effective automation prompt?
2. How do you use few-shot prompting with Copilot Chat?
3. Where do `.prompt.md` files live and how are they invoked?

---

## Exercises

**Exercise 9.1:** Write a `.prompt.md` file for generating Cypress component tests. Include the role, context, constraints, and output format sections.

**Exercise 9.2:** Create a chain-of-thought prompt for testing a multi-step wizard form. Observe how the step-by-step reasoning improves the quality of generated tests.

**Exercise 9.3:** Compare output quality: generate the same tests with a minimal prompt vs. a full structured prompt. Document the differences.

---

## Next Module

➡️ [Module 10: AI-Powered Test Generation](../module-10/README.md)
