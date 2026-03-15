---
title: "Module 10 — Prompt Files & Reusable Workflows"
module: 10
tags: [prompt-files, reusable-workflows, parameterization, chaining, team-sharing]
---

# Module 10 — Prompt Files & Reusable Workflows

## Learning Objectives

- Create and use `.prompt.md` files for repeatable tasks
- Understand prompt file metadata (mode, description, model)
- Build parameterised prompts with `${variables}`
- Chain prompt files into compound workflows
- Share prompt libraries across teams

---

## 10.1 What Are Prompt Files?

**Prompt files** (`.prompt.md`) are saved, versioned, reusable Copilot prompts stored in your
repository. Instead of typing the same detailed instructions repeatedly, you write them once,
commit them, and invoke them by name.

**Benefits:**
- Consistency across the team — everyone uses the same prompt quality
- Versioned with code — prompts evolve alongside the codebase
- Documented — prompts explain *why* they exist, not just *what* they do
- Discoverable — VS Code shows available prompt files in Chat

---

## 10.2 Prompt File Location & Naming

```
.github/
└── prompts/
    ├── generate-tests.prompt.md
    ├── fix-failing-test.prompt.md
    ├── generate-ci-pipeline.prompt.md
    ├── code-review.prompt.md
    └── bug-report.prompt.md
```

**Filename convention**: `<action>-<noun>.prompt.md` or `<noun>-<action>.prompt.md`

---

## 10.3 Prompt File Anatomy

```markdown
---
mode: "agent"          # "agent" | "ask" | "edit"
description: "Short description shown in the prompt picker"
model: "gpt-4o"        # Optional: override the default model
tools: ["readFile", "runCommand"]   # Optional: restrict/enable tools
---

# Prompt Title

Your prompt content here. You can use:

- ${selection}         — the currently selected code in the editor
- ${file}              — the current file path
- ${workspaceFolder}   — the root of the workspace
- Custom variables     — defined in the front-matter
```

### Mode Values

| Mode | Description |
|------|-------------|
| `agent` | Runs as an autonomous agent — can read/write files, run commands |
| `ask` | Opens a Chat conversation — analysis and Q&A only |
| `edit` | Applies edits to the selected code inline |

---

## 10.4 Using Prompt Files in VS Code

### Via Chat
```
Type "/" in the Chat panel → select a prompt from the dropdown
```

### Via Command Palette
```
Ctrl+Shift+P → "Copilot: Run Prompt"
```

### By Name
```
/generate-tests
/code-review
/fix-failing-test
```

---

## 10.5 Building Parameterised Prompts

Use variables to make prompts reusable across different contexts:

```markdown
---
mode: "agent"
description: "Migrate a test file to a new testing framework"
---

# Migrate Tests to New Framework

Migrate the test file at `${input:sourceFile:Path to the test file to migrate}` 
from `${input:fromFramework:Current framework (e.g., unittest)}` 
to `${input:toFramework:Target framework (e.g., pytest)}`.

Requirements:
- Preserve all test scenarios (do not skip or remove any test)
- Use native idioms of ${input:toFramework} (fixtures, parametrize, etc.)
- Update imports
- Run the migrated tests to confirm they all pass
```

---

## 10.6 Prompt Chaining — Compound Workflows

Chain prompt files to build sophisticated multi-step workflows.

### Automation Delivery Chain

```markdown
---
mode: "agent"
description: "Full automation delivery workflow: implement → test → review → pipeline"
---

# Full Automation Delivery

Execute the following steps in sequence for the feature described below.

## Feature
${selection}

## Step 1: Implement
Generate the production code for this feature following project conventions.
Reference: #file:.github/copilot-instructions.md

## Step 2: Test
Run the "Generate Tests" workflow (equivalent to /generate-tests) on the 
code you just wrote. Achieve ≥ 85% coverage.

## Step 3: Review
Run the "Code Review" workflow (equivalent to /code-review) on all 
changed files. Fix all CRITICAL and HIGH findings automatically.

## Step 4: Pipeline
Check if .github/workflows/ci.yml needs updating to run new tests.
Make any necessary pipeline changes.

## Step 5: Report
Write a summary of what was built, tested, and any open questions to 
.copilot-delivery-report.md
```

---

## 10.7 Prompt Library for Automation Teams

Here is a starter prompt library for a QA/Automation team:

### `analyse-coverage-gaps.prompt.md`
```markdown
---
mode: "ask"
description: "Identify untested code paths from a coverage report"
---
Analyse the coverage report at coverage.json (or the terminal output below)
and list the top 10 most risky uncovered code paths, ordered by:
1. Complexity (cyclomatic complexity estimate)
2. Frequency of change (check git log)
3. Business criticality (your assessment based on the code)

For each path, suggest the test scenarios that would cover it.

Coverage data: ${selection}
```

### `generate-performance-test.prompt.md`
```markdown
---
mode: "agent"
description: "Generate a k6 performance test for an API endpoint"
---
Generate a k6 performance test for the API endpoint described below.

Include:
- Ramp-up scenario (0 → 50 VUs over 30s)
- Sustained load (50 VUs for 2 minutes)
- Ramp-down (50 → 0 VUs over 30s)
- Thresholds: p95 < 500ms, error rate < 1%
- CSV output for reporting

Endpoint details: ${selection}
```

### `accessibility-test.prompt.md`
```markdown
---
mode: "agent"
description: "Generate Playwright accessibility tests using axe-core"
---
Generate Playwright accessibility tests for the page at ${input:url:Page URL}.

Use: @axe-core/playwright
Test for: WCAG 2.1 AA compliance
Fail on: critical and serious violations
Generate: detailed violation report

Follow the Page Object Model — create a new page object if one doesn't exist.
```

---

## 10.8 Sharing Prompt Libraries Across Teams

Since prompts live in `.github/prompts/`, they are:
- **Automatically available** to all team members who clone the repo
- **Versioned** alongside the code they support
- **Reviewable** via the normal PR process

**Team onboarding tip**: Add this to your `README.md`:
```markdown
## Copilot Prompts

Run `/` in Copilot Chat to see available team prompts:
- `/generate-tests` — Full test suite for selected code
- `/code-review` — Structured code review checklist
- `/fix-failing-test` — Diagnose and fix failing tests
- `/generate-ci-pipeline` — Create a CI/CD pipeline
- `/bug-report` — Generate a structured bug report
```

---

## Module Checkpoint

- [ ] Understood the difference between `agent`, `ask`, and `edit` modes
- [ ] Ran an existing prompt file from `.github/prompts/`
- [ ] Created a new parameterised prompt file for your team
- [ ] Chained 3 prompt files into a compound delivery workflow
- [ ] Added prompt library documentation to the project README

**Next**: [Module 11 — IntelliJ Deep Dive](./MODULE-11-intellij.md)
