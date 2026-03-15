---
title: "Module 09 — Multi-Session Strategies"
module: 9
tags: [multi-session, parallel-agents, session-management, handoff, persistence]
---

# Module 09 — Multi-Session Strategies

## Learning Objectives

- Run multiple agent sessions in parallel without conflicts
- Design effective session handoff protocols
- Persist context across session restarts
- Partition work across agents by responsibility
- Use the Sessions view in VS Code for orchestration

---

## 9.1 Why Multi-Session?

A single agent session is **synchronous and serial** — it does one thing at a time. Complex
automation projects benefit from **parallel sessions** where independent agents work
simultaneously.

**When multi-session matters:**

| Scenario | Benefit |
|----------|---------|
| Feature + tests + docs | 3x speed — agents work in parallel |
| Multi-service repo | Each agent owns one service |
| Frontend + backend | Agents work independently |
| Refactor + regression tests | Agent 1 refactors, Agent 2 tests simultaneously |

---

## 9.2 Partitioning Work — The Golden Rule

> **The Golden Rule of Parallel Agents**: Each agent must own a non-overlapping set of files.

```
✅ Good partition:
Agent 1: src/services/**  →  tests/services/**
Agent 2: src/api/**       →  tests/api/**
Agent 3: .github/**       →  docs/**

❌ Bad partition:
Agent 1: src/services/user_service.py
Agent 2: src/services/  (overlaps!)
```

---

## 9.3 Starting Parallel Sessions in VS Code

```
1. Open Copilot Chat panel
2. Start Agent 1 with Task A
3. Click the + icon in the Sessions panel to open Session 2
4. Start Agent 2 with Task B
5. Monitor both in the Sessions view
```

**Sessions View**: `Ctrl+Shift+P` → *Copilot: Open Sessions*

You can see:
- Active/paused/completed sessions
- Files each session has modified
- Commands each session has run
- Current step in the agentic loop

---

## 9.4 Structured Handoff Patterns

### Pattern 1: Sequential Handoff (relay race)

```
SESSION 1: "Build the UserService class"
  └─ Output: src/services/user_service.py

SESSION 2: "Generate tests for the UserService built by Session 1"
  └─ Input: #file:src/services/user_service.py
  └─ Output: tests/services/test_user_service.py

SESSION 3: "Update the CI pipeline to run the new UserService tests"
  └─ Input: #file:tests/services/test_user_service.py
  └─ Output: .github/workflows/ci.yml (updated)
```

### Pattern 2: Parallel Fan-Out / Fan-In

```
COORDINATOR SESSION
  "Split this feature into 3 parallel tasks, execute them, then merge"

TASK A (parallel) ──┐
TASK B (parallel) ──┼──▶ COORDINATOR SESSION: "Review and merge"
TASK C (parallel) ──┘
```

### Pattern 3: Review Chain

```
DEVELOPER SESSION: "Implement the payment gateway integration"
  └─ Output: changed files

REVIEWER SESSION (new): "Review the changes made by the developer session.
  Check for: security issues, test coverage, code quality. 
  Reference: #file:src/payments/ #file:tests/payments/"
  └─ Output: review report + automated fixes
```

---

## 9.5 Session Context Persistence

Copilot Agent sessions persist their conversation history across VS Code restarts.

**To explicitly save context:**

At the end of a session, ask Copilot:
```
Summarise what you accomplished in this session, what files you changed,
and what the next session should do to continue this task.
Save the summary to .copilot-session-state.md
```

At the start of the next session:
```
Read .copilot-session-state.md and continue from where the previous session left off.
```

**Pro tip**: Add `.copilot-session-state.md` to `.gitignore` so it's local-only.

---

## 9.6 Conflict Resolution Between Sessions

If two agents modify the same file, VS Code will show git conflicts. Resolve using Copilot:

```
These two agent sessions both modified the same file and created a conflict.
Here are the conflicting sections:

[paste conflict markers]

Merge them intelligently, preserving the intent of both changes.
```

---

## 9.7 Session Budget Management

Long-running agents consume context window tokens. Manage budgets:

**Signs a session needs to be restarted:**
- Responses become shorter or vague
- Agent starts ignoring instructions
- Agent repeats itself

**When to start a fresh session:**
- After completing a self-contained module
- When switching to a completely different domain (test code → infra code)
- After > 60 minutes of work on one task

**Session restart ritual:**
1. Ask the current session to write a handoff summary
2. Start a new session
3. Reference the handoff file + key source files

---

## 9.8 Team Multi-Session Workflows

For teams, multi-session extends across team members:

```
Team structure:
- Developer A: Feature implementation session (local)
- Developer B: Test automation session (local) 
- CI bot: Automated agent in GitHub Actions (cloud)
- Security bot: Security review agent triggered by PR (cloud)
```

All share the same `.github/copilot-instructions.md` and agent configs — one source of truth.

---

## 9.9 Anti-Patterns to Avoid

| Anti-pattern | Problem | Fix |
|-------------|---------|-----|
| Run 10 agents on overlapping files | Merge conflicts, unpredictable state | Strict file partitioning |
| Never restart long sessions | Context degradation, worse outputs | Time-box sessions (45–60 min) |
| No handoff documentation | Next agent starts from scratch | Always write session summaries |
| Agent with no exit condition | Infinite loop, resource waste | Define clear DONE criteria |
| Mix feature + debugging sessions | Muddied context, confused agent | One concern per session |

---

## Module Checkpoint

- [ ] Ran 2 parallel agent sessions on non-overlapping file sets
- [ ] Used the Sessions view to monitor and switch between active sessions
- [ ] Implemented a sequential handoff (Session 1 → Session 2 → Session 3)
- [ ] Saved session state to a handoff file and resumed in a new session
- [ ] Resolved a file conflict between two agent sessions using Copilot

**Next**: [Module 10 — Prompt Files & Reusable Workflows](./MODULE-10-prompt-files.md)
