---
title: "Lab 01 — Inline Completion Dojo"
lab: 1
duration: "45 minutes"
tags: [inline, completions, ghost-text, context-priming]
---

# Lab 01 — Inline Completion Dojo

## Objective

Master inline completions by completing 5 progressively harder exercises
using **only** ghost-text suggestions (no Chat, no Agent Mode).

---

## Setup

```bash
# Create a new Python file for this lab
touch lab01_completions.py
# Open it in your IDE with Copilot active
```

---

## Exercise 1 — Docstring-First (10 min)

Type the following docstring and signature, then let Copilot complete the body:

```python
def find_duplicates(items: list) -> list:
    """
    Return a list of elements that appear more than once in the input list.
    The result should be sorted and deduplicated.
    Empty list input returns empty list.
    
    Examples:
        find_duplicates([1, 2, 2, 3, 3, 3]) → [2, 3]
        find_duplicates([1, 2, 3]) → []
        find_duplicates([]) → []
    """
```

**Accept the suggestion. Run it manually to verify correctness.**

---

## Exercise 2 — Pattern Continuation (10 min)

Start this pattern and let Copilot continue all remaining validators:

```python
# Input validators return (is_valid: bool, error_message: str | None)

def validate_email(value: str) -> tuple[bool, str | None]:
    if not value:
        return False, "Email is required"
    if "@" not in value:
        return False, "Email must contain @"
    if "." not in value.split("@")[-1]:
        return False, "Email domain must have a dot"
    return True, None

def validate_phone(value: str) -> tuple[bool, str | None]:
    # Let Copilot complete this...
```

---

## Exercise 3 — Multi-Line Comment Block (10 min)

Generate a complete class from a comment block:

```python
##############################################################################
# OrderProcessor
#
# Processes customer orders through a state machine:
# PENDING → CONFIRMED → PICKED → SHIPPED → DELIVERED
# PENDING → CANCELLED (any time before PICKED)
#
# Methods:
#   confirm(order_id) → Order
#   cancel(order_id, reason) → Order  
#   advance_state(order_id) → Order
#   get_valid_transitions(current_state) → list[str]
#
# Raises: OrderNotFoundError, InvalidTransitionError
##############################################################################
```

---

## Exercise 4 — Type-Hint-Driven (10 min)

Write only the signature with complete type hints — Copilot generates the implementation:

```python
from datetime import datetime, timedelta
from typing import TypedDict

class ReportConfig(TypedDict):
    start_date: datetime
    end_date: datetime
    group_by: str  # "day" | "week" | "month"
    metrics: list[str]

def generate_report_summary(
    data: list[dict],
    config: ReportConfig,
    include_trend: bool = True
) -> dict[str, float | list[float]]:
```

---

## Exercise 5 — Alternative Suggestions (5 min)

Type the comment below, then press `Ctrl+Enter` to open the suggestions panel.
Review all alternatives and select the most efficient implementation:

```python
# Sort a list of dicts by multiple keys: first by 'priority' (desc), 
# then by 'created_at' (asc), then by 'name' (asc)
def multi_sort(items: list[dict]) -> list[dict]:
```

**Select the alternative that uses `functools` or `operator` rather than a lambda.**

---

## Lab Debrief Questions

1. Which exercise produced the highest quality suggestion on the first try? Why?
2. In Exercise 5, how many alternatives were generated? Were any dramatically different?
3. What did you learn about how type hints affect suggestion quality?
4. What would you add to a `copilot-instructions.md` based on this session?

---

## ✅ Completion Criteria

- [ ] All 5 exercises attempted
- [ ] At least 3 generated implementations manually verified as correct
- [ ] Alternatives panel explored in Exercise 5
- [ ] Lab debrief questions answered (in comments or a separate doc)
