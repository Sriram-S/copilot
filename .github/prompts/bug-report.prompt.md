---
mode: "ask"
description: "Generate a structured bug report from a failure description or stack trace"
---

# Bug Report Generator

Convert the information below into a **professional, structured bug report** ready to file as
a GitHub Issue.

## Input

**Failure description / stack trace / screenshot description**:
${selection}

---

## Output Format

Generate the following GitHub Issue body:

```markdown
## Bug Report

**Summary**: <one-line description of the defect>

**Severity**: Critical / High / Medium / Low
**Priority**: P0 / P1 / P2 / P3
**Component**: <module or service where the bug lives>
**Reported by**: Copilot Automation Agent
**Date**: <today's date>

---

### Environment
- OS: 
- Browser/Runtime version: 
- Application version / commit SHA: 
- Test environment: Dev / Staging / Production

---

### Steps to Reproduce
1. 
2. 
3. 

---

### Expected Behaviour
<What should have happened>

---

### Actual Behaviour
<What actually happened, including error messages>

---

### Logs / Stack Trace
\`\`\`
<paste stack trace here>
\`\`\`

---

### Root Cause Hypothesis
<Brief technical hypothesis — Copilot generated>

---

### Suggested Fix
<High-level fix approach — Copilot generated>

---

### Test Case to Prevent Regression
\`\`\`python
def test_<descriptive_name>():
    # Arrange
    
    # Act
    
    # Assert
\`\`\`

---

### Acceptance Criteria (Definition of Done)
- [ ] Bug is fixed and manually verified
- [ ] Regression test added and passing in CI
- [ ] No new failures introduced
- [ ] Documentation updated if behaviour change is user-facing
```
