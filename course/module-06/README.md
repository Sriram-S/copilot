# Module 06: Agent Mode & Custom Agents

## Learning Objectives

- Understand the difference between Chat mode, Edits mode, and Agent mode
- Use Agent mode to autonomously scaffold complete test automation projects
- Create custom `.agent.md` files for specialized automation tasks
- Understand agent tool use: file I/O, terminal, browser, search

---

## 6.1 The Three Modes of Copilot Chat

| Mode | What it does | Best for |
|---|---|---|
| **Chat** | Conversational responses, suggests code | Questions, explanations, code snippets |
| **Edits** | Applies changes directly to files you specify | Refactoring, updating multiple files |
| **Agent** | Autonomous: reads/writes files, runs terminal, installs packages | Full project scaffolding, complex multi-step tasks |

Switch modes using the mode toggle button in the Chat input bar (≡ icon).

---

## 6.2 Agent Mode in Depth

### 6.2.1 What Agent Mode Can Do

Agent mode gives Copilot access to **tools**:

| Tool | What it enables |
|---|---|
| `read_file` | Read any file in your workspace |
| `write_file` | Create or modify files |
| `run_in_terminal` | Execute commands (npm install, pytest, mvn test) |
| `search_workspace` | Find files and symbols |
| `fetch_webpage` | Read documentation URLs |
| `thinking` | Extended reasoning before responding |

### 6.2.2 Enabling Agent Mode

```json
// .vscode/settings.json
{
  "chat.agent.enabled": true
}
```

Then click the **≡** icon in the Chat panel and select **Agent**.

### 6.2.3 Agent Mode Safety: Approval Gates

Agent mode shows you every proposed action before executing:
- File creation/modification: Shows diff, click **Accept** or **Decline**
- Terminal commands: Shows the command, you approve or edit before running
- You can always click **Stop** to halt agent execution mid-task

---

## 6.3 Using Agent Mode for Test Automation

### 6.3.1 Scaffold a Complete Playwright Project

```
You are an expert test automation engineer. 

Scaffold a complete Playwright TypeScript project for testing an e-commerce website with:
1. Playwright configuration with 3 browsers and CI-optimized settings
2. Page Object Model pattern with BasePage
3. Pages: HomePage, LoginPage, ProductPage, CartPage, CheckoutPage
4. Test files: auth.spec.ts, search.spec.ts, cart.spec.ts, checkout.spec.ts
5. Test data factory using faker.js
6. Custom fixture for authenticated user sessions
7. Environment configuration via .env files
8. Allure reporter configuration
9. GitHub Actions workflow for CI

Create all files. Run npm install when done. Run the smoke tests to verify.
```

Agent will:
1. Create `playwright.config.ts`
2. Create all page objects
3. Create all test files
4. Create `package.json` with dependencies
5. Run `npm install`
6. Run `npx playwright test --grep @smoke`
7. Fix any failures it encounters

### 6.3.2 Analyze and Fix a Failing Test Suite

```
Analyze the test results in the last terminal run. For each failing test:
1. Read the relevant source files
2. Identify the root cause
3. Apply the fix
4. Re-run only the fixed tests to verify
Repeat until all tests pass.
```

### 6.3.3 Generate API Test Coverage Report

```
Scan @workspace for all API endpoint definitions (controllers, routes, OpenAPI spec). 
Compare against existing test files. 
Generate a coverage gap report as COVERAGE.md.
Then generate test files for all uncovered endpoints.
```

---

## 6.4 Custom Agents (`.agent.md` files)

Custom agents allow you to create **named, reusable AI personas** with specific knowledge, tools, and behaviors.

### 6.4.1 Where to Put Agent Files

```
.github/
└── agents/
    ├── test-generator.agent.md
    ├── reviewer.agent.md
    ├── api-tester.agent.md
    └── accessibility-tester.agent.md
```

### 6.4.2 Agent File Structure

```markdown
---
name: Test Generator
description: Generates comprehensive test suites following project conventions
tools:
  - read_file
  - write_file
  - run_in_terminal
  - search_workspace
---

[System prompt / instructions for the agent here]
```

### 6.4.3 Invoking Custom Agents

In VS Code Chat, switch to Agent mode and select your custom agent from the dropdown, or type:

```
@test-generator Generate tests for the PaymentService class
```

---

## 6.5 Building a Test Generator Agent

See `.github/agents/test-generator.agent.md` in this repository for the full implementation.

Key elements of an effective test-generator agent:

1. **Clear persona** – "You are a senior test automation engineer specializing in..."
2. **Framework knowledge** – Tell it which frameworks, patterns, and conventions to use
3. **Output format** – Specify file structure, naming, annotation requirements
4. **Quality gates** – Include assertions about coverage and edge cases
5. **Tool permissions** – List only the tools it needs (principle of least privilege)

---

## 6.6 The Reviewer Agent

A Reviewer agent provides code review for pull requests:

```markdown
---
name: Test Reviewer
description: Reviews test code for quality, coverage, and maintainability
tools:
  - read_file
  - search_workspace
---

Review the test files changed in this PR. Check for:
- Missing negative test cases
- Hardcoded test data (should use data factory)
- Missing waits that could cause flakiness
- Assertions that are too broad or too narrow
- Missing error handling
Provide a structured review with line-level comments.
```

---

## 6.7 The MCP Integration (Model Context Protocol)

Agent mode can also connect to external MCP servers, giving agents access to:
- **Jira/Azure DevOps** – Create test cases from requirements
- **Confluence** – Read acceptance criteria to generate tests  
- **GitHub** – Read issues and PRs to generate regression tests
- **BrowserTools MCP** – Direct browser control for web testing

Configure MCP servers in VS Code settings:

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

---

## Knowledge Check

1. What is the key difference between "Edits" mode and "Agent" mode?
2. Where do you place custom `.agent.md` files?
3. What MCP server would you use to automatically generate tests from Jira tickets?

---

## Exercises

**Exercise 6.1:** Switch to Agent mode and ask Copilot to scaffold a complete Playwright project from scratch. Observe each tool call it makes.

**Exercise 6.2:** Review the `test-generator.agent.md` in this repository. Modify it to add a rule about accessibility testing.

**Exercise 6.3:** Create a new custom agent for "performance test generation" that generates k6 or Locust scripts from API documentation.

---

## Next Module

➡️ [Module 07: Parallel Agents & Orchestration](../module-07/README.md)
