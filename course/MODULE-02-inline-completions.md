---
title: "Module 02 — Inline Completions Mastery"
module: 2
tags: [inline, completions, ghost-text, keyboard-shortcuts, context-priming]
---

# Module 02 — Inline Completions Mastery

## Learning Objectives

- Accept suggestions at word, line, and full-block granularity
- Use the suggestions panel for alternative completions
- Prime context with multiple open tabs
- Leverage multi-cursor for parallel completions
- Know every keyboard shortcut for the completion workflow

---

## 2.1 Granular Acceptance

Most engineers accept suggestions one way: press `Tab`. But there are three levels:

| Acceptance Level | Shortcut (Win/Linux) | Shortcut (macOS) | When to use |
|-----------------|---------------------|-----------------|-------------|
| Accept **word** | `Ctrl+→` | `Cmd+→` | Accept start, then decide |
| Accept **line** | `Alt+→` | `Option+→` | Accept partial suggestion |
| Accept **all** | `Tab` | `Tab` | Accept entire suggestion |

**Practice exercise**: generate a 10-line function and accept it one word at a time to understand the boundaries.

---

## 2.2 The Suggestions Panel

When Copilot generates a single suggestion, it may have computed up to **10 alternatives**.
View them all:

- **VS Code**: `Ctrl+Enter` opens the Copilot suggestions panel with all alternatives
- **IntelliJ**: `Alt+]` / `Alt+[` cycles through alternatives

**Workflow tip**: Cycle through alternatives before accepting. The first suggestion is not always the best.

---

## 2.3 Context Priming via Open Tabs

Copilot uses your **open editor tabs** as additional context. Use this intentionally:

```
Strategy: Before writing a new module, open:
├── The interface/ABC this module implements
├── One existing module as a reference implementation
└── The test file you expect to fill in later
```

**Demo**: Open `tests/users/test_user_service.py` (even if empty) while writing
`src/users/user_service.py`. Copilot will generate code that matches the testability
pattern implied by the test file.

---

## 2.4 Multi-Line Comment Blocks

A block comment above a function is the most reliable completion trigger:

```python
##############################################################################
# UserRepository
#
# Provides CRUD operations for the User entity against a PostgreSQL database.
# All methods are async. Connection pooling is handled externally via SQLAlchemy.
#
# Methods:
#   get_by_id(user_id: UUID) -> User | None
#   get_by_email(email: str) -> User | None
#   create(data: CreateUserDTO) -> User
#   update(user_id: UUID, data: UpdateUserDTO) -> User
#   delete(user_id: UUID) -> None
#   list(page: int, page_size: int) -> list[User]
##############################################################################
class UserRepository:
```

Copilot will generate the complete class with all six methods documented.

---

## 2.5 Repetitive Pattern Completion

Copilot excels at continuing patterns. Show it the shape once:

```python
ROUTES = {
    "users":    Route("/api/v1/users",    methods=["GET", "POST"]),
    "user":     Route("/api/v1/users/{id}", methods=["GET", "PUT", "DELETE"]),
    "orders":   Route("/api/v1/orders",   methods=["GET", "POST"]),
    # Copilot continues from here with all remaining routes
```

---

## 2.6 Using Next Edit Suggestions (VS Code 1.99+)

**Next Edit Suggestions** predicts your *next* edit based on what you just changed — not just
what follows your cursor.

**How it works**: After you edit one function, Copilot anticipates that you need to update:
- Related callers
- Tests
- Documentation

A yellow indicator appears in the gutter. Press `Tab` to jump to and accept the next suggested edit.

**Enable it**:
```json
{
  "github.copilot.nextEditSuggestions.enabled": true
}
```

---

## 2.7 Language-Specific Tips

### Python
- Type the function signature with full type hints — Copilot uses them heavily
- Use `"""docstring"""` not `# comment` — docstrings are higher-weight context

### TypeScript / JavaScript
- Define your interface/type first, then implement — Copilot generates conforming code
- Use JSDoc `@param` and `@returns` annotations

### Java
- Write the Javadoc before the method signature
- Copilot recognises Spring annotations and generates Spring-compliant code

### SQL
- Comment the purpose and expected result set; Copilot generates complete queries
- Open your ORM model file in an adjacent tab

---

## 2.8 When to NOT Accept Completions

| Situation | What to do |
|-----------|-----------|
| Generated code calls a non-existent method | Dismiss and refine your prompt |
| Completion has magic numbers or hard-coded values | Accept but immediately refactor |
| Suggestion omits error handling | Accept, then use Inline Chat `/fix` to add it |
| Performance looks wrong (nested loops over large data) | Open Chat, explain the data size |

---

## Module Checkpoint

- [ ] Practised word-level, line-level, and full acceptance
- [ ] Viewed the suggestions panel and selected an alternative
- [ ] Primed context with multiple open tabs and noticed the difference
- [ ] Used multi-line comment blocks to generate a full class
- [ ] Enabled and tried Next Edit Suggestions

**Next**: [Module 03 — Copilot Chat & Slash Commands](./MODULE-03-chat-slash-commands.md)
