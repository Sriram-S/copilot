---
title: "Module 04 — Agent Mode — Autonomous Coding"
module: 4
tags: [agent-mode, autonomous, agentic-loop, tool-use, multi-file]
---

# Module 04 — Agent Mode — Autonomous Coding

## Learning Objectives

- Activate and configure Agent Mode in VS Code and IntelliJ
- Understand the agentic loop: plan → act → observe → self-correct
- Write effective agent prompts for complex automation tasks
- Monitor, pause, and guide running agents
- Know when to use Agent Mode vs. Chat vs. inline completions

---

## 4.1 What Makes Agent Mode Different

| Feature | Chat | Agent Mode |
|---------|------|-----------|
| Can read files | Only if referenced | ✅ Autonomously searches |
| Can write files | ✅ (with confirmation) | ✅ (with confirmation) |
| Can run commands | ❌ | ✅ |
| Multi-step planning | ❌ | ✅ |
| Self-corrects on errors | ❌ | ✅ |
| Persistent across restarts | ❌ | ✅ |
| Can call tools/APIs | ❌ | ✅ |

**Agent Mode is for tasks that span multiple files and require iteration** — not for
simple Q&A or quick refactors.

---

## 4.2 Activating Agent Mode

### VS Code
1. Open Copilot Chat (`Ctrl+Alt+I`)
2. Click the model selector dropdown at the top of the Chat panel
3. Select **"Agent"** (or toggle the ⚡ icon)

Or via settings:
```json
{
  "github.copilot.chat.agentMode": true
}
```

### IntelliJ
1. Open Copilot Chat
2. Click the **"Agent"** toggle in the Chat panel header
3. Available from plugin version 1.5+

---

## 4.3 The Agentic Loop

When you give an agent a task, it follows this loop:

```
┌─────────────────────────────────────────────────┐
│                   PLAN                          │
│  Agent analyses the task and creates a plan     │
│  (you can review and approve before it starts)  │
└──────────────────┬──────────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────────┐
│                    ACT                          │
│  Execute one step (read file, write code, run   │
│  command, call API, search web)                 │
└──────────────────┬──────────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────────┐
│                  OBSERVE                        │
│  Read the result of the action                  │
│  (file contents, command output, error message) │
└──────────────────┬──────────────────────────────┘
                   │
          ┌────────┴────────┐
          │                 │
       Success           Failure
          │                 │
          ▼                 ▼
    Next step         Self-correct
                    (analyse error,
                     try different
                     approach)
```

---

## 4.4 Writing Effective Agent Prompts

### Structure of a Great Agent Task

```
CONTEXT:    What is the project/feature/constraint?
GOAL:       What exact outcome do you want?
CONSTRAINTS: What should the agent NOT do?
DEFINITION OF DONE: How do you verify success?
```

### Example: Test Suite Generation

```
CONTEXT: This is a Python FastAPI service with a UserService class in 
src/services/user_service.py. Tests use pytest and are in tests/.

GOAL: Generate a comprehensive test suite for UserService. Cover:
- All public methods (get_user, create_user, update_user, delete_user)
- Happy paths and error paths
- Mock the UserRepository dependency

CONSTRAINTS: 
- Do not modify the production code
- Do not use real database connections
- Test file must be at tests/services/test_user_service.py

DEFINITION OF DONE: pytest tests/ passes with 0 failures and ≥ 85% coverage
on user_service.py
```

### Example: CI Pipeline Creation

```
CONTEXT: Python FastAPI project, poetry for dependencies, pytest for tests,
Dockerised with docker-compose. Target: GitHub Actions.

GOAL: Create a complete CI/CD pipeline that:
1. Runs linting (ruff, mypy) on every PR
2. Runs unit tests with coverage report as PR annotation
3. Builds and pushes Docker image to GHCR on merge to main
4. Deploys to staging (fly.io) after successful image push

CONSTRAINTS:
- All Actions pinned to SHA
- No secrets hard-coded
- Must include Dependabot configuration

DEFINITION OF DONE: The workflow YAML is valid, Dependabot config exists,
and a README section explains which secrets to add
```

---

## 4.5 Tool Use — What Agents Can Do

Agents have access to a set of **tools** they invoke autonomously:

| Tool | What it does |
|------|-------------|
| `readFile` | Read any file in the workspace |
| `writeFile` | Create or overwrite a file |
| `runCommand` | Execute a terminal command |
| `searchCode` | Search the codebase (like `@workspace`) |
| `fetchWebPage` | Read a URL (documentation, issues) |
| `createPR` | Open a pull request on GitHub |
| `searchIssues` | Search GitHub issues |

You can see which tools are available and toggle them in the agent panel.

---

## 4.6 Monitoring and Controlling an Agent

### Sessions View (VS Code)

Open the **Sessions** view (`Ctrl+Shift+P` → *Copilot: Open Sessions*) to:
- See all active and completed agent sessions
- Pause / resume an agent
- Review every action the agent took
- Export session history

### Intervention Points

Agents ask for approval before:
- Running terminal commands (first time; can be set to auto-approve)
- Writing to files outside the workspace
- Calling external APIs

**Best practice**: Review the plan the agent presents before approving. Ask it to adjust if needed.

### Stopping an Agent

Click **Stop** in the Chat panel or Sessions view. The agent saves its state — you can resume.

---

## 4.7 Parallel Agent Sessions

You can run **multiple agents simultaneously** for different tasks:

```
Agent 1: "Generate unit tests for the authentication module"
Agent 2: "Update the CI pipeline to run integration tests"
Agent 3: "Refactor the legacy data-import script to use async I/O"
```

**Rule of thumb**: Keep agents in non-overlapping file sets to avoid conflicts.

---

## 4.8 Real-World Automation Agent Task Examples

```
# 1. End-to-End Feature Implementation
"Implement a password reset flow: API endpoint, email template, 
rate limiting, and full test coverage. Follow the patterns in 
the existing user registration feature."

# 2. Legacy Code Modernisation
"Migrate all callback-style async code in src/legacy/ to 
async/await. Run tests after each file to confirm no regression."

# 3. Security Audit & Fix
"Scan all SQL queries in the codebase for injection vulnerabilities.
Fix each one using parameterised queries. Add a test for each fix."

# 4. Performance Optimisation
"Find all N+1 database queries in src/api/. Fix them with eager loading
or batching. Assert response time < 200ms in the integration tests."
```

---

## Module Checkpoint

- [ ] Activated Agent Mode in your IDE
- [ ] Ran an agent to generate a complete test file
- [ ] Reviewed the agent's plan before approving
- [ ] Used the Sessions view to monitor agent progress
- [ ] Ran 2 agents in parallel on non-overlapping tasks
- [ ] Successfully stopped and resumed an agent

**Next**: [Module 05 — Custom Instructions & .md Files](./MODULE-05-custom-instructions.md)
