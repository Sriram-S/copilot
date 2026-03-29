# Module 05: Copilot Chat Interface Deep Dive

## Learning Objectives

- Master all Copilot Chat participants, variables, and slash commands
- Use `@workspace` for cross-file test analysis
- Attach test failure screenshots and logs for AI diagnosis
- Leverage conversation history and follow-up prompts effectively

---

## 5.1 The Chat Architecture

Copilot Chat operates on a **context window**. The context is composed of:

```
┌─────────────────────────────────────────────┐
│           CONTEXT WINDOW                    │
│  ┌─────────────────────────────────────┐    │
│  │  System prompt + Custom instructions│    │
│  ├─────────────────────────────────────┤    │
│  │  Referenced files (#file, #codebase)│    │
│  ├─────────────────────────────────────┤    │
│  │  Current editor content              │    │
│  ├─────────────────────────────────────┤    │
│  │  Conversation history               │    │
│  ├─────────────────────────────────────┤    │
│  │  Your current message               │    │
│  └─────────────────────────────────────┘    │
└─────────────────────────────────────────────┘
```

Understanding what is in context at any moment is key to getting accurate responses.

---

## 5.2 Chat Participants

### `@workspace`
Performs a semantic search across your entire project. Copilot reads your file structure and retrieves the most relevant files for your query.

**Best for:**
- "Are there any tests that cover the payment flow?"
- "What Page Objects exist in this project?"
- "Where is the test data factory defined?"

```
@workspace Find all tests that use the AdminPage class and 
summarize what user actions they cover.
```

### `@vscode`
Queries VS Code's API, settings, and documentation.

```
@vscode How do I configure Playwright Test to run in parallel 
across 4 workers and show a visual report?
```

### `@terminal`
Provides context from the active terminal (errors, output).

```
@terminal The last test run failed. Explain what caused the 
failure and suggest a fix.
```

---

## 5.3 Context Variables

### `#file`
Attach a specific file as context. Copilot reads the full file content.

```
Generate Playwright tests for all methods in #file:src/pages/CartPage.ts
```

### `#selection`
Include whatever text you have selected in the editor.

```
Explain what this locator strategy does and suggest a more resilient alternative: #selection
```

### `#codebase`
A deep semantic search – more thorough than `@workspace`.

```
#codebase Are there any tests that test the user profile update feature? 
If not, generate them.
```

### `#terminalLastCommand`
The output of the last terminal command.

```
#terminalLastCommand What caused these test failures? Provide fixes for each.
```

---

## 5.4 Slash Commands in Detail

### `/tests` – The Core Command for Automation Engineers

Select any function, class, or module and run `/tests`:

```
/tests Generate comprehensive tests for this CartService class.
Include:
- Unit tests for each method
- Edge cases (empty cart, negative quantities, out of stock)
- Data-driven tests using @DataProvider / pytest.mark.parametrize
```

### `/explain` – Understanding Legacy Automation Code

```
/explain What does this XPath expression do and why might it 
break on different browsers?
```

### `/fix` – Repairing Test Failures

```
/fix The test is failing with: 
"TimeoutError: page.waitForSelector: Timeout 30000ms exceeded"
Here is the test: #selection
```

### `/doc` – Documenting Your Framework

```
/doc Generate JSDoc documentation for all public methods 
in this Page Object class
```

---

## 5.5 Vision: Analyzing Test Failure Screenshots

With `"github.copilot.chat.visionEnabled": true` in VS Code, you can attach images:

1. Take a screenshot of a failing test or unexpected UI state
2. Drag the image into the Copilot Chat input, or click the attachment icon (📎)
3. Describe what you need:

```
This screenshot shows the actual result of my checkout test. 
The expected result is a confirmation page. What went wrong 
based on the UI state shown?
```

**Use cases:**
- Analyze Playwright `on-failure` screenshots
- Compare expected vs actual UI state
- Identify broken selectors from screenshots

---

## 5.6 Effective Conversation Patterns

### Pattern 1: Scaffolding + Refinement

```
Turn 1: "Generate a basic Playwright test for the login flow"
Turn 2: "Add negative test cases: invalid email, locked account, wrong password"
Turn 3: "Extract these tests into a describe block with beforeEach for navigation"
Turn 4: "Add data-driven parametrize for all credential combinations"
```

### Pattern 2: Explain → Fix → Verify

```
Turn 1: "/explain Why does this test flake on CI but pass locally?"
Turn 2: "/fix Apply the fixes you suggested"
Turn 3: "How would I write a regression test to ensure this never flakes again?"
```

### Pattern 3: Discovery → Generation

```
Turn 1: "@workspace What API endpoints are NOT covered by tests?"
Turn 2: "Generate tests for the three endpoints you identified"
Turn 3: "Add authentication headers and error response tests to each"
```

---

## 5.7 Chat for CI/CD Test Analysis

When tests fail in CI, copy the failure output and paste into Chat:

```
Our GitHub Actions pipeline failed with these test results:

[Paste CI output here]

Please:
1. Identify all failing tests and their root causes
2. Classify failures as: flaky, environment, code bug, or missing setup
3. Suggest fixes for each category
4. Propose guardrails to prevent these in future runs
```

---

## 5.8 IntelliJ Chat Differences

In IntelliJ IDEA, the Chat panel is accessed via **View → Tool Windows → GitHub Copilot Chat**.

Key differences:
- The `@vscode` participant is not available (use `@workspace` and `#file` instead)
- Use **Code Actions** (`Alt+Enter`) for inline test generation
- The **Diff view** in IntelliJ is used to preview suggested changes before applying

---

## Knowledge Check

1. What is the difference between `@workspace` and `#codebase`?
2. How would you attach a Playwright failure screenshot to a Chat conversation?
3. Write a three-turn conversation pattern to go from a blank file to a complete, data-driven API test suite.

---

## Exercises

**Exercise 5.1:** Open a project with an existing Page Object. Use `@workspace` to ask Copilot which methods have no test coverage. Then generate the missing tests.

**Exercise 5.2:** Intentionally break a test (wrong selector). Run it, capture the error. Use `#terminalLastCommand` to ask Copilot to explain and fix the failure.

**Exercise 5.3:** Use Vision to compare two screenshots (expected vs actual UI state) and ask Copilot to identify the regression.

---

## Next Module

➡️ [Module 06: Agent Mode & Custom Agents](../module-06/README.md)
