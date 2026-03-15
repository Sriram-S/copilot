---
mode: "ask"
description: "Perform a thorough code review of the selected code"
---

# Code Review

Perform a thorough **code review** of the following code. Evaluate it across these dimensions:

## Review Dimensions

### 1. Correctness
- Does the logic correctly implement the stated intent?
- Are there off-by-one errors, null pointer risks, or incorrect comparisons?
- Are all error paths handled?

### 2. Security
- Are inputs validated and sanitised?
- Are secrets/credentials absent from the code?
- Are there injection risks (SQL, shell, XSS)?
- Does the code follow the principle of least privilege?

### 3. Performance
- Are there obvious O(n²) algorithms that could be O(n log n)?
- Are database queries N+1?
- Is memory allocated and released correctly?
- Are there unnecessary blocking I/O calls?

### 4. Maintainability
- Is the code readable without inline comments explaining *what* (not *why*)?
- Are functions/methods single-responsibility?
- Is there duplication that should be extracted?
- Are magic numbers replaced with named constants?

### 5. Test Coverage
- Are all branches testable?
- Are there untestable constructs (deep nesting, hidden dependencies)?
- What test cases are missing?

## Output Format

Produce a structured review:

```markdown
## Code Review Summary

**Overall Rating**: 🟢 Approve / 🟡 Approve with Suggestions / 🔴 Request Changes

### Critical Issues (must fix)
- [ ] ...

### Major Issues (should fix)
- [ ] ...

### Minor Issues / Suggestions
- [ ] ...

### Positive Observations
- ...

### Suggested Test Cases to Add
- ...
```

## Code Under Review

${selection}
