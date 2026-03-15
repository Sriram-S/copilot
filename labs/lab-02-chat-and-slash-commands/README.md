---
title: "Lab 02 — Chat Command Bootcamp"
lab: 2
duration: "60 minutes"
tags: [chat, slash-commands, workspace, terminal, context-variables]
---

# Lab 02 — Chat Command Bootcamp

## Objective

Use every major Copilot Chat command and participant against the starter code
provided in this lab. No Agent Mode — Chat and Inline Chat only.

---

## Starter Code

Create the following file to use as your subject:

```python
# lab02_subject.py
import time
import json
from datetime import datetime

def process_data(data, config):
    results = []
    for item in data:
        if item['type'] == 'A':
            r = item['value'] * config['multiplier']
            results.append({'id': item['id'], 'result': r, 'ts': datetime.now().isoformat()})
        elif item['type'] == 'B':
            r = item['value'] + config['offset']
            results.append({'id': item['id'], 'result': r, 'ts': datetime.now().isoformat()})
    return json.dumps(results)


def get_user(user_id, db):
    time.sleep(0.1)  # simulate DB latency
    query = f"SELECT * FROM users WHERE id = {user_id}"
    return db.execute(query)


class DataCache:
    def __init__(self):
        self.cache = {}
    
    def get(self, key):
        return self.cache[key]
    
    def set(self, key, value):
        self.cache[key] = value
    
    def invalidate(self, key):
        del self.cache[key]
```

---

## Exercise 1 — /explain (10 min)

Select the entire `process_data` function. Open Inline Chat (`Ctrl+I`):

```
/explain What does this function do? What are its inputs and outputs? 
What are the edge cases it doesn't handle?
```

**Record the edge cases Copilot identifies.**

---

## Exercise 2 — /fix (15 min)

### Fix 1: SQL Injection
Select the `get_user` function. Run:
```
/fix This function has a SQL injection vulnerability. Fix it using parameterised queries.
```

### Fix 2: Missing Error Handling
Select the `DataCache.get` method:
```
/fix This method raises a KeyError when the key doesn't exist. 
Fix it to return None for missing keys and add a method to check if a key exists.
```

### Fix 3: Performance Issue
Select the entire class:
```
/fix The cache has no size limit or TTL (time-to-live). Add LRU eviction 
with a configurable max size and optional TTL in seconds.
```

---

## Exercise 3 — /tests (15 min)

Select the fixed `DataCache` class. Open Chat and run:
```
/tests Generate a pytest test suite for DataCache. Cover:
- Basic get/set
- Key not found (should return None)
- LRU eviction when max size reached
- TTL expiry
- Thread safety (concurrent access)
Use time freezing for TTL tests (freezegun).
```

---

## Exercise 4 — /doc (5 min)

Select the `process_data` function. Run:
```
/doc Add a comprehensive Google-style docstring including:
- Description
- Args (with types)
- Returns
- Raises
- Example usage
```

---

## Exercise 5 — @workspace (10 min)

Open a new Chat conversation (don't use Inline Chat). Try these:

```
@workspace What files in this project have functions without type annotations?
@workspace Are there any print statements used for debugging?
@workspace What is the overall test coverage of this project?
```

---

## Exercise 6 — @terminal (5 min)

1. Run this command in the terminal: `python -c "from lab02_subject import process_data; process_data(None, {})"` (it will fail)
2. In Copilot Chat: `@terminal Explain the error and tell me how to fix it`

---

## Exercise 7 — Context Variables (chained)

```
# In a single Chat session, chain these:
1. "Refactor process_data to use a strategy pattern based on item type. Reference #file:lab02_subject.py"
2. "Now add type annotations to the refactored version"
3. "Generate tests for the refactored version that cover all strategies"
4. "/doc Add documentation to the strategy classes"
```

---

## ✅ Completion Criteria

- [ ] Used /explain, /fix, /tests, /doc on real code
- [ ] SQL injection fixed with parameterised query
- [ ] DataCache has LRU eviction and TTL
- [ ] Tests generated and passing
- [ ] @workspace queries answered correctly
- [ ] @terminal used to diagnose an error
- [ ] Context chain completed (3+ follow-ups in one session)
