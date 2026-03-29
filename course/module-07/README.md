# Module 07: Parallel Agents & Orchestration

## Learning Objectives

- Understand how parallel agent execution works in VS Code
- Design multi-agent workflows for large test automation tasks
- Orchestrate agents across different concerns (generation, review, CI)
- Understand agent communication patterns and hand-offs

---

## 7.1 What Are Parallel Agents?

Parallel agents allow Copilot to **spin up multiple specialized sub-agents simultaneously**, each working on a different aspect of a task, then synthesizing results.

Think of it like a QA team:
- One engineer writes UI tests
- One engineer writes API tests  
- One engineer writes performance tests
- A tech lead reviews and integrates everything

**All at the same time.**

---

## 7.2 When Parallel Agents Are Used

Copilot's agent mode automatically uses parallel sub-agents when:

1. The task can be decomposed into independent sub-tasks
2. Multiple files need to be analyzed simultaneously
3. The "thinking" tool is enabled and the model reasons through parallelizable work
4. You explicitly instruct the agent to work in parallel

### Enabling Parallel Tool Calls

```json
// .vscode/settings.json
{
  "chat.agent.enabled": true,
  "github.copilot.chat.agent.thinkingTool": true
}
```

---

## 7.3 Visualizing Parallel Agent Execution

```
User Prompt: "Generate complete test coverage for all 5 API modules"
            |
            v
    +-------------------+
    |   Orchestrator    |
    |      Agent        |
    +--------+----------+
             | Spawns parallel sub-agents
    +--------+------------------------------------------+
    |        |           |         |                    |
    v        v           v         v                    v
+-------+ +-------+ +-------+ +-------+          +-------+
| Auth  | | Users | |Orders | | Prods |          |  Pay  |
| Tests | | Tests | | Tests | | Tests |          | Tests |
+-------+ +-------+ +-------+ +-------+          +-------+
    |        |           |         |                    |
    +--------+-----------+---------+--------------------+
                          |
                          v
                  +------------------+
                  | Integration &    |
                  | Validation       |
                  +------------------+
```

---

## 7.4 Prompts That Trigger Parallel Execution

### 7.4.1 Explicit Parallelism

```
Work in parallel on the following tasks simultaneously:

Task A: Generate Playwright UI tests for all pages in src/pages/
Task B: Generate API tests for all routes defined in src/routes/
Task C: Generate contract tests between UI and API layers
Task D: Create a test execution matrix in tests/TEST-MATRIX.md

Once all tasks complete, run the full test suite and fix any failures.
```

### 7.4.2 Module-Based Parallelism

```
Generate test coverage for our e-commerce platform. 
Process all modules in parallel:
- src/modules/auth/ -> tests/auth/
- src/modules/catalog/ -> tests/catalog/
- src/modules/cart/ -> tests/cart/
- src/modules/checkout/ -> tests/checkout/
- src/modules/payment/ -> tests/payment/

Each module should have unit, integration, and E2E tests.
Maintain consistent patterns across all generated test files.
```

---

## 7.5 Multi-Agent Workflow Patterns

### Pattern 1: Generate → Review → Fix

```
Agent 1 (Generator): Creates all test files
        |
        v (hands off generated files)
Agent 2 (Reviewer):  Reviews for quality issues, coverage gaps
        |
        v (hands off review report)  
Agent 3 (Fixer):     Applies review suggestions, runs tests
        |
        v
Agent 4 (Reporter):  Generates coverage report and CI config
```

Orchestrate this in a single prompt:

```
Execute this multi-phase workflow:

PHASE 1 - Generate:
  Create comprehensive Playwright tests for src/pages/*.ts
  Save generated tests to tests/generated/

PHASE 2 - Review:  
  Review the generated tests for:
  - Missing negative test cases
  - Hardcoded values (should use fixtures)
  - Missing assertions
  Append issues to tests/generated/REVIEW.md

PHASE 3 - Fix:
  Apply all issues from REVIEW.md to the test files

PHASE 4 - Validate:
  Run npx playwright test and show results
  Update README.md with coverage summary
```

### Pattern 2: Parallel Feature Coverage

```
You are orchestrating test generation for a banking app.
Work in parallel streams:

STREAM 1 (Authentication):
  - Login with MFA tests
  - Password reset flow tests  
  - Session timeout tests
  - Account lockout tests

STREAM 2 (Transactions):
  - Transfer money tests
  - Bill payment tests
  - Transaction history tests
  - Recurring payment tests

STREAM 3 (API Layer):
  - Account balance API tests
  - Transfer API tests
  - Fraud detection API tests

Complete all streams then run: npm test
```

### Pattern 3: Cross-Platform Parallel Testing

```
Generate the same test scenarios in three frameworks simultaneously:

Target feature: User registration and email verification flow

Framework A: Playwright TypeScript -> tests/playwright/
Framework B: Cypress JavaScript -> tests/cypress/
Framework C: RestAssured Java -> tests/api/

For each framework:
1. Create page objects (or API client)
2. Create test data factory
3. Create the test file
4. Add framework-specific configuration

After generation, compare the approaches and add COMPARISON.md
```

---

## 7.6 Agent Orchestration with `.agent.md` Files

You can create orchestration agents that delegate to specialized agents:

```markdown
---
name: Test Suite Orchestrator
description: Orchestrates comprehensive test suite generation across multiple agents
tools:
  - read_file
  - write_file
  - run_in_terminal
  - search_workspace
---

You are a test orchestration manager. When asked to generate tests:

1. First, analyze the codebase structure using search_workspace
2. Identify these categories of work:
   - UI test generation (delegate to ui-test persona)
   - API test generation (delegate to api-test persona)
   - Performance test generation (delegate to perf-test persona)
3. Work on independent categories in parallel
4. Sequence dependent work appropriately
5. Validate all generated tests by running them
6. Generate a final COVERAGE_REPORT.md

Always show your execution plan before starting.
```

---

## 7.7 Real-World Parallel Agent Example: E-Commerce Regression Suite

Imagine you have a new feature: "Product Wishlist". Here's a parallel agent workflow:

```
New feature 'Wishlist' has been merged. Generate a comprehensive 
regression test suite.

Parallelize this work:

[UI Tests - Run in parallel with API Tests]:
  Read src/components/Wishlist.tsx and src/pages/WishlistPage.tsx
  Generate Playwright tests covering:
  - Add to wishlist from product page
  - View wishlist page
  - Remove from wishlist
  - Wishlist persists after page refresh
  - Wishlist sync across devices (mocked)
  Save to: tests/e2e/wishlist.spec.ts

[API Tests - Run in parallel with UI Tests]:
  Read src/api/wishlist.controller.ts and its OpenAPI spec
  Generate supertest/REST-assured tests covering:
  - POST /api/wishlist/items (add item)
  - GET /api/wishlist (list items)  
  - DELETE /api/wishlist/items/:id
  - Error cases: item not found, unauthorized, duplicate
  Save to: tests/api/wishlist.api.spec.ts

[After both complete]:
  Run the complete test suite
  Generate test coverage report
  Update src/TESTING.md with wishlist test documentation
```

---

## 7.8 Limitations and Best Practices

### Current Limitations
- Parallel agents share the same workspace, so file conflicts are possible
- Long-running agents may time out on very large codebases
- Each agent call consumes quota; use wisely

### Best Practices
1. **Decompose clearly** – Define boundaries between parallel tasks upfront
2. **Use explicit file paths** – Avoid ambiguity about where to write output
3. **Sequence dependencies** – Don't parallelize tasks where B depends on A's output
4. **Validate incrementally** – Run tests after each major phase
5. **Keep prompts focused** – One parallel task = one clear, scoped concern

---

## Knowledge Check

1. What setting enables the "thinking" tool that allows agents to reason about task decomposition?
2. When should you NOT parallelize agent tasks?
3. Design a 3-agent parallel workflow for testing a new "checkout with saved address" feature.

---

## Exercises

**Exercise 7.1:** Write a parallel agent prompt that generates UI tests, API tests, and a coverage report for a given feature simultaneously.

**Exercise 7.2:** Create an orchestration `.agent.md` file that automatically decomposes any test request into parallel workstreams.

**Exercise 7.3:** Run a parallel agent workflow on a real project. Observe the tool calls. Note which tasks Copilot chose to parallelize vs. sequence.

---

## Next Module

➡️ [Module 08: Custom Instructions](../module-08/README.md)
