# Module 13: Advanced & Latest Copilot Features (2024–2025)

## Learning Objectives

- Understand the latest Copilot features released in 2024-2025
- Use Copilot Edits for multi-file refactoring across large test suites
- Apply the thinking tool for complex test architecture decisions
- Leverage Copilot's vision capabilities for UI testing workflows
- Use MCP servers to connect Copilot to Jira, Confluence, and external tools

---

## 13.1 Copilot Edits Mode

Edits mode is designed for **multi-file changes** where you specify which files to modify and Copilot applies coordinated edits across all of them simultaneously.

### When to Use Edits Mode vs. Agent Mode

| | Edits Mode | Agent Mode |
|---|---|---|
| **File scope** | You specify exactly which files | Agent decides which files to touch |
| **Autonomy** | You control the files, Copilot writes diffs | Agent is fully autonomous |
| **Best for** | Known refactoring across known files | Open-ended tasks with unknown scope |
| **Approval** | Preview diff before accepting | Approve each action step |

### Activating Edits Mode

1. Click the mode toggle (≡) in Copilot Chat
2. Select "Edits"
3. Add files using the "Add files" button (+)
4. Write your request

### Test Suite Refactoring with Edits

```
[Add files: src/pages/BasePage.ts, src/pages/LoginPage.ts, 
           tests/auth.spec.ts, tests/checkout.spec.ts]

Refactor these files to migrate from Playwright's deprecated 
page.$() selector syntax to the modern Locator API:

1. Replace page.$('.selector') with page.locator('.selector')
2. Replace page.$$('.selector') with page.locator('.selector').all()
3. Replace await element.click() with await page.locator(...).click()
4. Replace page.waitForSelector() with await expect(page.locator()).toBeVisible()

Apply changes to all added files. Preserve all test logic.
```

---

## 13.2 The Thinking Tool

When enabled (`"github.copilot.chat.agent.thinkingTool": true`), Copilot reasons through complex problems before responding. This is particularly valuable for:

- Test architecture decisions
- Debugging complex failure patterns
- Designing test strategies for complex systems

### Example: Complex Architecture Decision

```
[Thinking tool activated]

We are building tests for a microservices application with 12 services.
Each service has its own test suite. The services communicate via:
- REST APIs (synchronous)
- Kafka messages (asynchronous)
- gRPC (internal)

Design a comprehensive test strategy that covers:
1. Unit tests per service
2. Contract tests between services (specify which tool: Pact, Spring Cloud Contract)
3. Integration tests with real Kafka (specify: TestContainers vs mock)
4. End-to-end tests for critical user journeys (specify: how to handle async)
5. Performance tests (specify: k6 vs Gatling)

Think through the tradeoffs carefully. Show your reasoning.
```

Copilot will show extended reasoning (like Chain-of-Thought) before giving the architectural recommendation.

---

## 13.3 Vision Capabilities

Copilot can analyze images attached to chat. For test automation:

### 13.3.1 Analyzing Failing Test Screenshots

```
[Attach screenshot: test-failure-checkout.png]

This is a screenshot from our failing Playwright test. 
The test expected to see the order confirmation page 
but this is what was captured instead.

1. Describe what's wrong in the UI
2. What might have caused this?
3. Write the Playwright assertion that would have caught this specific issue
4. Suggest selector strategies for the elements visible in this screenshot
```

### 13.3.2 Generating Selectors from UI Screenshots

```
[Attach screenshot: login-page.png]

Generate a complete Page Object class for this login page.
For each interactive element you can see:
1. Suggest the best Playwright selector strategy
2. Name the locator property appropriately
3. Create the corresponding action method

Output: src/pages/LoginPage.ts
```

### 13.3.3 Visual Regression Baseline Creation

```
[Attach screenshot: home-page-expected.png]

Generate a Playwright visual test that:
1. Navigates to the home page
2. Masks dynamic elements (prices, banners, user-specific content)
3. Takes a screenshot
4. Compares against a baseline with 2% threshold tolerance
5. Saves the baseline as: tests/visual/baselines/home-page.png

Use: toHaveScreenshot() with appropriate options
```

---

## 13.4 Model Context Protocol (MCP) for Testing

MCP servers extend Copilot's capabilities by connecting it to external systems.

### 13.4.1 Jira MCP Integration

```json
// .vscode/settings.json
{
  "mcp": {
    "servers": {
      "jira": {
        "command": "npx",
        "args": ["@modelcontextprotocol/server-atlassian"],
        "env": {
          "JIRA_BASE_URL": "https://yourorg.atlassian.net",
          "JIRA_API_TOKEN": "${env:JIRA_API_TOKEN}"
        }
      }
    }
  }
}
```

**With Jira MCP connected:**
```
Fetch all acceptance criteria from Jira ticket QA-1234.
Generate Playwright tests for every acceptance criterion.
Name each test with the acceptance criterion ID (AC-1, AC-2, etc.)
Link the test back to the Jira ticket using a test comment.
```

### 13.4.2 GitHub MCP for Regression Detection

```json
{
  "mcp": {
    "servers": {
      "github": {
        "command": "npx",
        "args": ["@modelcontextprotocol/server-github"],
        "env": {
          "GITHUB_PERSONAL_ACCESS_TOKEN": "${env:GITHUB_TOKEN}"
        }
      }
    }
  }
}
```

**With GitHub MCP:**
```
Fetch the code diff from PR #456.
Analyze what changed and identify:
1. Which existing tests might be broken by this change
2. What new tests are needed to cover the changed code
3. Generate those new tests
```

### 13.4.3 BrowserTools MCP for Direct Browser Control

The BrowserTools MCP server gives Copilot direct browser control, enabling it to:
- Navigate to pages and observe the DOM
- Capture screenshots autonomously
- Generate selectors from live elements
- Run audits (accessibility, performance, SEO)

```
Using the BrowserTools MCP, navigate to http://staging.example.com/checkout.
For each form field you discover:
1. Record the best locator strategy
2. Generate the Page Object method
3. Note any accessibility violations

Output: src/pages/CheckoutPage.ts
```

---

## 13.5 Copilot Workspace (GitHub.com Feature)

Copilot Workspace is available on GitHub.com and allows you to:
- Open an issue and have Copilot generate a complete implementation plan
- Execute the plan with full file creation across your repo
- Iterate on the plan before committing

**For test automation:**
1. Create a GitHub issue: "Add tests for the new wishlist feature"
2. Open in Copilot Workspace
3. Copilot generates a test implementation plan
4. Review and iterate the plan
5. Execute to create all test files in a new branch
6. Open a PR

---

## 13.6 Copilot Extensions

Copilot Extensions are third-party extensions that appear as chat participants (`@extension-name`).

| Extension | Use in Testing |
|---|---|
| `@sentry` | Analyze production errors to generate regression tests |
| `@datadog` | Analyze monitoring data to identify test gaps |
| `@postman` | Generate Playwright/RestAssured tests from Postman collections |
| `@browserstack` | Run tests on BrowserStack devices from Copilot Chat |

---

## 13.7 Next Edit Suggestions (Advanced)

NES goes beyond simple autocomplete:

- **Cross-file prediction**: Makes a change in LoginPage.ts, NES predicts the test update needed in auth.spec.ts
- **Refactoring chains**: Rename a method, NES cascades through all usages
- **Pattern propagation**: Add a new pattern to one test, NES suggests applying it to similar tests

Enable and navigate with `Tab` (accept predicted location) + `Tab` again (accept edit).

---

## 13.8 Copilot in VS Code Notebook (Jupyter)

For data-driven testing and test analysis:

```
/newNotebook

Create a Jupyter notebook that:
1. Loads our Playwright test results JSON from: playwright-report/results.json
2. Visualizes: pass rate over time, most failing tests, slowest tests
3. Identifies flaky tests (tests that sometimes pass, sometimes fail)
4. Generates a PDF report for stakeholders

Use: pandas, matplotlib, plotly
```

---

## 13.9 Feature Comparison: VS Code vs. IntelliJ (2025)

| Feature | VS Code | IntelliJ |
|---|---|---|
| Inline completions | ✅ | ✅ |
| Chat panel | ✅ | ✅ |
| Agent mode | ✅ Full | ✅ Full |
| Edits mode | ✅ | Partial |
| Parallel agents | ✅ | Coming soon |
| Thinking tool | ✅ | ✅ |
| Vision (images) | ✅ | ✅ |
| `.prompt.md` files | ✅ | Partial |
| `.agent.md` files | ✅ | Coming soon |
| MCP servers | ✅ | Coming soon |
| Copilot Extensions | ✅ | Roadmap |
| NES (Next Edit Suggestions) | ✅ | ✅ |
| Workspace instructions | ✅ | ✅ |

---

## Knowledge Check

1. What is the key difference between Edits mode and Agent mode?
2. How do MCP servers extend Copilot's capabilities for test automation?
3. What can you do with Vision capabilities that isn't possible with text-only prompts?

---

## Exercises

**Exercise 13.1:** Use Edits mode to refactor all your tests to use a new base class. Add 3 files to the editing session and apply a coordinated change.

**Exercise 13.2:** Attach a failing test screenshot to Copilot Chat. Ask it to generate the assertion that would have caught the failure.

**Exercise 13.3:** Set up the GitHub MCP server and use it to generate tests from a real PR's diff.

---

## Next Module

➡️ [Module 14: Best Practices & Real-World Patterns](../module-14/README.md)
