---
mode: "agent"
description: "Generate a comprehensive test suite for the selected file or function"
---

# Generate Test Suite

You are an expert test automation engineer. Generate a **comprehensive test suite** for the
code provided below (or currently selected in the editor).

## Requirements

1. **Coverage**: Produce tests for every public method/function:
   - Happy path (valid inputs, expected outputs)
   - Edge cases (empty collections, zero values, maximum lengths)
   - Boundary conditions (off-by-one, max int, min date)
   - Error paths (invalid inputs, missing required fields, network failure simulation)
   - Concurrency/race conditions where applicable

2. **Framework**: Use the framework already present in the project (`pytest`, `Jest`, `JUnit 5`).
   If none exists yet, default to `pytest` for Python, `Jest` for TypeScript.

3. **Structure**:
   - One `describe` block (or test class) per function/method under test
   - Descriptive test names as full sentences
   - AAA comments (`# Arrange`, `# Act`, `# Assert`) inside each test
   - Fixtures/factories for complex data — no inline literals

4. **Mocking**: Mock all I/O boundaries (HTTP, DB, filesystem, time) using the standard mock
   library for the language. Do **not** mock internal logic.

5. **Output location**: Place the generated file in `tests/` mirroring the source path.
   E.g., `src/users/service.py` → `tests/users/test_service.py`.

## Context

${selection}

---

After generating the tests, run them and report:
- How many tests were created
- Current pass/fail status
- Any gaps in coverage you recommend addressing next
