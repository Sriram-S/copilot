---
title: "Module 12 — Advanced Combinations"
module: 12
tags: [advanced, power-user, combinations, uncommon-features, productivity-multipliers]
---

# Module 12 — Advanced Combinations

## Learning Objectives

- Combine multiple Copilot features for exponential productivity
- Use lesser-known features most engineers miss
- Build personal productivity systems around Copilot
- Understand model selection and when to switch models
- Create the ultimate Copilot-powered automation workflow

---

## 12.1 Power Combination Matrix

The most impact comes from combining features. Here's the combination matrix:

| Combination | Power Level | Use Case |
|-------------|------------|----------|
| Custom Instructions + Agent Mode | ⭐⭐⭐⭐⭐ | Agents that know your entire codebase convention |
| Prompt Files + Multi-Session | ⭐⭐⭐⭐⭐ | Repeatable parallel delivery workflows |
| Custom Agent + Skills | ⭐⭐⭐⭐⭐ | Domain expert agents with playbooks |
| @workspace + Inline Chat | ⭐⭐⭐⭐ | Explore then refactor in context |
| Next Edit Suggestions + Inline | ⭐⭐⭐⭐ | Cascade edits across related files |
| Agent Mode + @terminal | ⭐⭐⭐⭐ | Run-fix-iterate loop without leaving IDE |
| /tests + Coverage Report | ⭐⭐⭐⭐ | Targeted test generation for gaps only |
| Bug Report Prompt + /fix | ⭐⭐⭐ | Structured fix-and-document workflow |

---

## 12.2 The Ultimate Automation Engineer Workflow

Here is the end-to-end daily workflow of a top-1% Copilot automation engineer:

### Morning: Context Priming (5 min)

```bash
# 1. Open key context files in tabs
code .github/copilot-instructions.md
code src/services/ tests/services/

# 2. Ask workspace for today's context
@workspace What are the open issues and failing tests?
@workspace Which modules have the lowest test coverage?
```

### Feature Work: TDD with Agent

```
1. Write interface/signature with full docstring
2. Start Agent Mode with test-engineer persona:
   "@test-engineer Generate tests for the signature I just wrote"
3. Watch agent generate tests (review plan before approving)
4. Run tests — all fail (correct!)
5. Start second Agent: "Implement this function to pass all tests"
6. Verify both agents' output, merge
```

### Code Review: Automated Multi-Lens Review

```
1. Select all changed files
2. Run /code-review (security lens)
3. Run /tests on any new functions without tests
4. Ask: "What are the 3 highest-risk things in my changes today?"
```

### End of Day: Documentation + CI Check

```
1. Run /doc on any new public APIs
2. Ask agent: "Update CHANGELOG.md with today's changes"
3. Check CI: @terminal "Explain any CI failures from today"
```

---

## 12.3 Model Selection — When to Switch

Copilot offers multiple models. Know when to use each:

| Model | Best For | Avoid When |
|-------|----------|-----------|
| Claude Sonnet | Long context (large files, many files) | Quick completions |
| GPT-4o | Balanced quality/speed, default choice | Very long documents |
| o3-mini / o1 | Complex reasoning, algorithm design | Simple boilerplate |
| Gemini | Multimodal tasks (if image context needed) | Code generation |

**Switch model in Chat**: Click the model name in the Chat header dropdown.

---

## 12.4 Uncommon Features Most Engineers Miss

### Feature 1: Symbol References in Chat

```
Instead of: "Look at the UserService class"
Use: "Look at #symbol:UserService"
```

Copilot navigates the actual symbol definition — not a file search.

### Feature 2: Copilot Edits (Multi-File Simultaneous Edit)

Different from Agent Mode — **Edits mode** applies a single transformation across multiple
files at once:

```
1. Open Copilot Chat
2. Click "Open in Edits" (pencil icon)
3. Add the files you want to edit
4. Type: "Replace all usages of the legacy Logger class with the new StructuredLogger"
5. Review a diff of ALL changes before accepting
```

### Feature 3: Inline Chat on Diffs

In a PR diff view (VS Code with GitHub Pull Requests extension):
```
1. Open a PR diff
2. Select a line in the diff
3. Ctrl+I → ask Copilot to explain or suggest improvement
```

### Feature 4: Notebook Cell Completions

Copilot works inside Jupyter notebooks — each cell gets completions:
```python
# In a notebook cell:
# Load the test_results.csv and plot a trend chart showing 
# daily pass rate over the last 30 days using matplotlib
import pandas as pd
# Copilot completes the entire analysis
```

### Feature 5: Terminal Autocomplete

In VS Code integrated terminal (experimental):
```bash
# Type a partial command — Copilot suggests the completion
pytest tests/ --  # Copilot suggests: --cov=src --cov-report=html -v
```

Enable:
```json
{
  "github.copilot.terminal.enabled": true
}
```

### Feature 6: Quick Question (Inline, No Chat Panel)

Select code → right-click → "Copilot: Quick Question" (IntelliJ) for instant answers
without opening the full Chat panel.

---

## 12.5 Instruction Layering Deep Dive

You can have up to 4 layers of instructions applied simultaneously:

```
User settings instructions
    +
.github/copilot-instructions.md (repo-wide)
    +
.github/instructions/test-automation.instructions.md (if editing a test file)
    +
.github/instructions/api-automation.instructions.md (if editing an API file)
    =
Copilot applies ALL simultaneously
```

**Advanced**: Use different instruction files for different tech stacks in a monorepo:

```
.github/instructions/
├── python.instructions.md      (applyTo: "**/*.py")
├── typescript.instructions.md  (applyTo: "**/*.ts,**/*.tsx")
├── java.instructions.md        (applyTo: "**/*.java,**/*.kt")
└── sql.instructions.md         (applyTo: "**/*.sql,migrations/**")
```

---

## 12.6 Building a Team Copilot Playbook

The most advanced thing you can do: turn your team's entire engineering knowledge into
a Copilot-accessible system.

```
.github/
├── copilot-instructions.md              # Company-wide rules
├── instructions/
│   ├── security.instructions.md         # Security team rules
│   ├── frontend.instructions.md         # Frontend team rules
│   └── data.instructions.md             # Data team rules
├── prompts/
│   ├── onboard-new-service.prompt.md    # New microservice scaffold
│   ├── quarterly-security-audit.prompt.md
│   └── performance-regression-check.prompt.md
└── agents/
    ├── backend-engineer.md
    ├── frontend-engineer.md
    ├── data-engineer.md
    └── sre.md
```

**This is your unfair advantage** — new team members are immediately productive because
Copilot knows all your standards.

---

## 12.7 Measuring Copilot ROI

Track these metrics before and after adopting the techniques in this course:

| Metric | How to Measure |
|--------|---------------|
| Time to first passing test | Timer from PR open to green CI |
| Test coverage delta per feature | Coverage report before/after |
| PR review cycles | GitHub Insights: PR cycle time |
| Time to diagnose + fix a bug | Track in issue tracker |
| Onboarding time for new engineers | Survey |

**Benchmark**: Teams that implement all techniques from this course report:
- 40–60% reduction in time to write tests
- 30–50% fewer bug escapes to production
- 2–3x faster onboarding for new automation engineers

---

## Module Checkpoint

- [ ] Combined Custom Instructions + Agent Mode for a full feature
- [ ] Switched models mid-session appropriately
- [ ] Used Copilot Edits (multi-file mode) for a large refactor
- [ ] Enabled and tested terminal autocomplete
- [ ] Built a layered instruction set for a multi-language repo
- [ ] Documented your team's Copilot playbook in `.github/`

**Next**: [Module 13 — Capstone Project](./MODULE-13-capstone.md)
