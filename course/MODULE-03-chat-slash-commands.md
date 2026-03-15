---
title: "Module 03 — Copilot Chat & Slash Commands"
module: 3
tags: [chat, slash-commands, participants, workspace, context-variables]
---

# Module 03 — Copilot Chat & Slash Commands

## Learning Objectives

- Use every slash command fluently
- Leverage `@workspace`, `@vscode`, `@terminal` participants
- Reference files, symbols, and selections in Chat
- Chain commands for compound tasks
- Use Chat history effectively across a session

---

## 3.1 Opening Copilot Chat

| IDE | How to open |
|-----|------------|
| VS Code | `Ctrl+Alt+I` or click the Copilot icon in the sidebar |
| IntelliJ | `Alt+Shift+C` or **Tools → GitHub Copilot → Open Chat** |

The Chat panel maintains **session history** — each follow-up is aware of the conversation so far.

---

## 3.2 Slash Commands Reference

### `/explain`
**Purpose**: Understand complex code you didn't write.

```
/explain What does this function do and why does it use a recursive approach?
```

**Tip**: Select the code first, then run `/explain` — Copilot will reference the exact selection.

---

### `/fix`
**Purpose**: Fix a bug, compile error, or failing test.

```
/fix The function raises a KeyError when the config dict is missing the 'timeout' key
```

```
/fix [paste stack trace here]
```

**Automation pattern**: Use after running tests via the terminal — paste the failure output.

---

### `/tests`
**Purpose**: Generate test cases for selected code.

```
/tests Generate unit tests for this function using pytest. Include edge cases for empty input and None.
```

---

### `/doc`
**Purpose**: Add or improve documentation.

```
/doc Add a comprehensive docstring to this class following Google style
```

---

### `/new`
**Purpose**: Create a new file or project scaffold.

```
/new Create a pytest conftest.py for a FastAPI application with fixtures for:
- A test database using SQLite
- An authenticated test client
- A mock SMTP server
```

---

### `/newNotebook`
**Purpose**: Create a Jupyter notebook.

```
/newNotebook Create a data analysis notebook that loads test_results.csv, 
generates a pass/fail trend chart, and exports a HTML report
```

---

### `/runCommand` (VS Code)
**Purpose**: Generate and optionally run a terminal command.

```
/runCommand Run all tests that match the pattern 'test_user' and output results to test-results.xml
```

---

## 3.3 Chat Participants (@mentions)

### `@workspace`
Searches your entire repository for relevant context.

```
@workspace Where is the database connection configured?
@workspace Which files handle JWT authentication?
@workspace Find all places where the UserRepository is used
```

**Automation use case**: Understand a new codebase in minutes before writing automation.

---

### `@vscode`
Controls and queries VS Code itself.

```
@vscode How do I configure the Python test runner to use pytest?
@vscode What extensions are recommended for this project?
@vscode Open the settings for the GitHub Copilot extension
```

---

### `@terminal`
Understands your terminal state (last command, output, working directory).

```
@terminal Explain this error  
@terminal Fix the command I just ran
```

**Workflow**: Run a failing test → switch to Chat → type `@terminal fix the failing test`

---

## 3.4 Context Variables

### `#file:path`
Reference a specific file:
```
Refactor the logging in #file:src/services/payment_service.py to use structured JSON logs
```

### `#symbol:ClassName`
Reference a specific symbol:
```
Generate tests for #symbol:PaymentService
```

### `#selection`
Use the currently selected text in Chat (implicit when you open Chat with text selected).

### `#codebase`
Tell Copilot to search the full codebase (similar to `@workspace` but used inline):
```
How is error handling done in #codebase?
```

---

## 3.5 Compound Command Patterns for Automation

### Pattern 1: Analyse → Fix → Test
```
Step 1: /explain [select failing code]
Step 2: /fix Based on your explanation, the issue is in the null check
Step 3: /tests Generate regression tests for the exact bug scenario you just fixed
```

### Pattern 2: Explore → Generate → Verify
```
Step 1: @workspace Find all API endpoints that are not covered by integration tests
Step 2: /tests Generate integration tests for the endpoints you found
Step 3: /runCommand Run the new tests and show me the results
```

### Pattern 3: Read → Refactor → Document
```
Step 1: /explain #file:src/legacy/processor.py
Step 2: Refactor this into smaller, testable functions
Step 3: /doc Add comprehensive documentation to each function
```

---

## 3.6 Chat Best Practices

| Do | Don't |
|----|-------|
| Be specific: `Fix the null pointer on line 47` | Be vague: `fix the bug` |
| Reference files: `in #file:x.py` | Assume Copilot knows which file |
| Build on the conversation: `Now add error handling to the function you just wrote` | Start fresh for related tasks |
| Ask for explanations: `Explain why you chose this approach` | Accept code blindly |
| Use `@workspace` first to explore | Hard-code file paths if you don't know them |

---

## Module Checkpoint

- [ ] Used `/explain`, `/fix`, `/tests`, `/doc`, `/new` on real code
- [ ] Used `@workspace` to navigate an unfamiliar project
- [ ] Used `@terminal` to fix a command-line error
- [ ] Chained 3+ commands in a single Chat session for a compound task
- [ ] Used `#file:` to reference a specific file in a prompt

**Next**: [Module 04 — Agent Mode — Autonomous Coding](./MODULE-04-agent-mode.md)
