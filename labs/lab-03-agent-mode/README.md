---
title: "Lab 03 — Agent Mode Sprint"
lab: 3
duration: "60 minutes"
tags: [agent-mode, autonomous, agentic-loop, multi-file, self-correction]
---

# Lab 03 — Agent Mode Sprint

## Objective

Complete 3 progressively complex Agent Mode tasks. Each task requires
the agent to read files, make edits, run commands, and self-correct —
all without you writing a single line of code.

---

## Prerequisites

- Agent Mode activated in Copilot Chat
- A terminal available in your IDE
- Python 3.11+ installed

---

## Task 1 — Implement a Feature End-to-End (20 min)

### Your prompt to the agent:

```
CONTEXT: This is a Python project. There is no existing code yet.

GOAL: Implement a simple Rate Limiter with these specs:
- Class: RateLimiter
- Algorithm: Token bucket
- Config: max_requests (int), window_seconds (int)
- Method: is_allowed(client_id: str) -> bool
  - Returns True if the client is within rate limit
  - Returns False if the rate limit is exceeded
  - Thread-safe implementation required
- All code in: src/rate_limiter.py

CONSTRAINTS:
- No external libraries (stdlib only)
- Include type annotations
- Include a complete docstring

DEFINITION OF DONE:
- src/rate_limiter.py exists and has no syntax errors
- Run: python -c "from src.rate_limiter import RateLimiter; print('OK')" succeeds
```

**Watch the agent**: Note how it plans, creates files, runs verification, and self-corrects.

---

## Task 2 — Generate Tests + Fix Failures (25 min)

After Task 1 completes, start a NEW agent session:

```
CONTEXT: src/rate_limiter.py was just created (read it first).

GOAL: Generate and run a comprehensive test suite.

STEP 1: Read src/rate_limiter.py to understand the implementation.

STEP 2: Generate tests/test_rate_limiter.py with these scenarios:
- Single client within limit
- Single client exceeds limit
- Multiple clients (independent limits)
- Limit resets after window expires
- Thread safety (10 concurrent threads, each making 5 requests)
- Invalid inputs (negative max_requests, zero window)

STEP 3: Run: pytest tests/test_rate_limiter.py -v

STEP 4: If any tests fail, diagnose and fix them (either the test or the implementation).

STEP 5: Achieve 100% pass rate before stopping.

DEFINITION OF DONE: pytest tests/test_rate_limiter.py passes with 0 failures.
```

---

## Task 3 — CI Pipeline + Linting (15 min)

Start a THIRD agent session:

```
CONTEXT: We have src/rate_limiter.py and tests/test_rate_limiter.py.

GOAL: Set up the project properly and create CI.

STEP 1: Create pyproject.toml with:
- Python 3.11 minimum
- pytest, pytest-cov as dev dependencies
- ruff and mypy for linting

STEP 2: Create .github/workflows/ci.yml that:
- Runs on push and PR to main
- Installs dependencies
- Runs ruff (fail on any issues)
- Runs mypy (fail on any type errors)  
- Runs pytest with coverage (fail if < 90%)
- Posts coverage as a job summary

STEP 3: Run ruff and mypy locally and fix any issues they find.

STEP 4: Verify pytest --cov=src --cov-fail-under=90 passes.

DEFINITION OF DONE: All local checks pass. CI YAML is valid (use yamllint).
```

---

## Lab Debrief

After completing all 3 tasks, answer:

1. How many self-correction loops did the agent perform across all tasks?
2. Which task required the most intervention from you? Why?
3. What would you add to `.github/copilot-instructions.md` to reduce
   agent errors in future runs of this lab?
4. What was the total time saved vs. writing everything manually?

---

## ✅ Completion Criteria

- [ ] `src/rate_limiter.py` exists with full implementation
- [ ] `pytest tests/test_rate_limiter.py` passes with 0 failures
- [ ] Coverage ≥ 90%
- [ ] `.github/workflows/ci.yml` exists and is valid YAML
- [ ] `ruff check .` produces no errors
- [ ] `mypy src/` produces no errors
- [ ] Lab debrief questions answered
