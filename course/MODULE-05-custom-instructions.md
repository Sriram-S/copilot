---
title: "Module 05 — Custom Instructions & .md Files"
module: 5
tags: [custom-instructions, copilot-instructions, instruction-files, scoped-rules, markdown]
---

# Module 05 — Custom Instructions & .md Files

## Learning Objectives

- Understand the three levels of Copilot instruction injection
- Write effective `copilot-instructions.md` files
- Create scoped `.instructions.md` files for specific directories
- Use personal instructions for user-level preferences
- Measure the quality impact of well-crafted instructions

---

## 5.1 The Three Levels of Instructions

```
Level 1 (Broadest):  User settings instructions
                     → Applies to every Copilot interaction for this user
                     
Level 2 (Project):   .github/copilot-instructions.md  
                     → Applies to all interactions in this repository
                     
Level 3 (Scoped):    .github/instructions/*.instructions.md
                     → Applies only to files matching the applyTo glob
```

All three levels are **additive** — Copilot merges them all.

---

## 5.2 The Global Project File — `copilot-instructions.md`

**Location**: `.github/copilot-instructions.md`  
**Effect**: Injected into every Copilot interaction in this repo — completions, chat, and agents.

### What to Put In It

```markdown
# Project Context
Brief description of what the project does, the tech stack, 
and the team's goals. (2–4 sentences)

# Coding Standards
Language-specific rules: naming conventions, error handling 
patterns, preferred libraries.

# Architecture
Key patterns: layered architecture, repository pattern, 
event-driven, etc.

# Testing Strategy
Test framework, coverage targets, mock/stub strategy.

# Security Rules
Input validation, no hard-coded secrets, preferred auth method.

# Vocabulary
Define domain terms so Copilot uses them consistently.
```

### What NOT to Put In It

- Secrets or credentials (never)
- Highly volatile information (versions that change weekly)
- More than ~500 words (diminishing returns; focus on the highest-value rules)

---

## 5.3 Scoped Instructions — `*.instructions.md`

**Location**: `.github/instructions/`  
**Filename**: `<topic>.instructions.md`  
**Activation**: Via the `applyTo` front-matter field

### Front-Matter Syntax

```yaml
---
applyTo: "tests/**,**/*.test.ts,**/*.spec.ts"
---
```

**`applyTo`** accepts glob patterns separated by commas. The file's rules apply only when
Copilot is working on a file matching the pattern.

### Example: Test-Specific Instructions

```markdown
---
applyTo: "tests/**"
---

# Test File Rules
- Use pytest fixtures over setup/teardown methods
- Every test must have exactly ONE assertion concept
- Test names must be full sentences describing the scenario
- Mock time using freezegun, never datetime.now() directly
```

### Example: Database Migration Instructions

```markdown
---
applyTo: "migrations/**,alembic/**"
---

# Migration Rules
- Migrations must be reversible (include downgrade())
- Never DROP COLUMN in a migration — deprecate and archive instead
- Test migrations against a copy of production schema weekly
- Add a comment at the top: date, author, JIRA ticket
```

---

## 5.4 User-Level Instructions (Personal)

Set in VS Code settings (not committed to the repo):

```json
{
  "github.copilot.chat.codeGeneration.instructions": [
    {
      "text": "I prefer functional programming patterns over OOP where possible."
    },
    {
      "text": "Always include type annotations in Python."
    },
    {
      "text": "I use Black formatter — do not suggest autopep8."
    }
  ]
}
```

Or reference a file:
```json
{
  "github.copilot.chat.codeGeneration.instructions": [
    {
      "file": "/Users/me/.config/copilot/personal-instructions.md"
    }
  ]
}
```

---

## 5.5 Writing High-Impact Instructions

### The PACT Framework

Each rule in your instructions should pass the **PACT** test:

| Letter | Meaning | Example |
|--------|---------|---------|
| **P**recise | No ambiguity | `Use httpx, not requests` ✅ vs `use a good HTTP library` ❌ |
| **A**ctionable | Copilot can follow it | `Add type hints to all functions` ✅ |
| **C**onsistent | Compatible with other rules | Don't mix OOP and functional requirements |
| **T**estable | You can verify compliance | `Every method must have a docstring` ✅ |

### Anti-Patterns to Avoid

| Anti-pattern | Why it fails | Better alternative |
|-------------|-------------|-------------------|
| "Write clean code" | Too vague | "Functions ≤ 20 lines; extract if longer" |
| "Be secure" | Too broad | "Validate all user inputs with Pydantic" |
| "Follow best practices" | Subjective | "Use the repository pattern for DB access" |
| "Don't make mistakes" | Useless | Specific error-handling rules |

---

## 5.6 Instruction File Recipes for Automation Teams

### Automation Framework Instructions

```markdown
---
applyTo: "framework/**"
---

# Test Framework Architecture Rules
- All page objects inherit from BasePage
- Selectors must be stored in a separate `locators.py` file per page
- Never use time.sleep() — use explicit waits only
- Log every major action at INFO level with the element description
- Screenshot on failure via a pytest fixture — not inline in tests
```

### Data Fixtures Instructions

```markdown
---
applyTo: "tests/fixtures/**,tests/factories/**"
---

# Test Data Rules
- All factories use the factory_boy library
- Never use real PII — use Faker for generated names, emails, etc.
- Fixture files are YAML, not JSON (better multiline support)
- Each factory must define a `_create` and `_build` strategy
```

---

## 5.7 Verifying Your Instructions Are Working

To check Copilot is applying your instructions, open Chat and ask:

```
@workspace What are the test-writing rules for this project?
```

If Copilot can recite your instruction rules back, it is applying them.

You can also test directly: generate code that should trigger your rule and verify it complies.

---

## Module Checkpoint

- [ ] Created or reviewed `.github/copilot-instructions.md` in your project
- [ ] Created at least 2 scoped `.instructions.md` files with `applyTo` globs
- [ ] Set user-level personal instructions in VS Code settings
- [ ] Verified instructions are applied by asking Copilot to recite them
- [ ] Measured before/after quality improvement on a test generation task

**Next**: [Module 06 — Custom Agents & Skills](./MODULE-06-custom-agents.md)
