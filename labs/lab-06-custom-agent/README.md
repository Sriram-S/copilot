---
title: "Lab 06 — Build Your Automation Agent"
lab: 6
duration: "60 minutes"
tags: [custom-agent, agent-config, skills, persona, tools]
---

# Lab 06 — Build Your Automation Agent

## Objective

Design and deploy a custom Copilot agent tailored to your team's automation domain.
By the end, you will have an agent that outperforms the generic Copilot for your
specific use case.

---

## Phase 1 — Agent Design (15 min)

Answer these questions before writing any files:

| Question | Your Answer |
|----------|------------|
| What is this agent's primary domain? | e.g., "API test automation for payment systems" |
| What framework/language does it specialise in? | e.g., "Python + httpx + pytest" |
| What are its 3 key capabilities? | |
| What should it NEVER do? | |
| What triggers should activate a skill? | |
| When should it escalate to a human? | |

---

## Phase 2 — Write the Agent Config (20 min)

Create `.github/agents/my-automation-agent.md` using the structure from Module 06.

Your agent config must include:

```markdown
# <Agent Name>

## Identity
[2-3 sentences describing who this agent is and its expertise]

## Primary Capabilities
- [Capability 1]
- [Capability 2]  
- [Capability 3]
- [Capability 4]
- [Capability 5]

## Workflow
[Step-by-step procedure: how does the agent approach a task?]

## Tools Permitted
- readFile
- writeFile
- runCommand (restricted to: pytest, pip, git, ruff, mypy)
- searchCode

## Coding Standards
[Reference or repeat the key standards for your domain]

## Non-Goals
[What this agent must NOT do]

## Escalation Criteria
[Explicit list of when to stop and ask a human]

## Skills
- [Skill 1]
- [Skill 2]
```

---

## Phase 3 — Write a Skill (15 min)

Create a skill playbook for your agent's most common task.

Example: `.github/agents/skills/generate-api-regression-suite.md`

```markdown
# Skill: Generate API Regression Suite

## Trigger
When asked to create regression tests for an API endpoint or service.

## Prerequisites
- Read the API contract (OpenAPI spec or route file)
- Check for existing tests in tests/ to avoid duplication

## Steps
1. Extract all endpoints from the spec/router
2. For each endpoint, identify:
   - Success scenarios (one per valid use case)
   - Validation failure scenarios (one per required field)
   - Auth failure scenarios (if protected)
   - Not-found scenarios (if applicable)
3. Generate test file following AAA structure
4. Run tests: `pytest tests/ -k <new_test_file> -v`
5. Fix any import or assertion errors
6. Report: test count, pass rate, recommended additions

## Output
- Test file in tests/api/test_<resource>.py
- Terminal output showing all tests passing
```

---

## Phase 4 — Test Your Agent (10 min)

### Activation Test 1: Basic Task

In Copilot Chat, invoke your agent and give it a simple task:
```
@my-automation-agent Generate a simple test for the calculate_discount function
in #file:lab01_completions.py (from Lab 01)
```

Verify:
- [ ] Agent adopted the persona described in your config
- [ ] Agent followed the workflow steps you defined
- [ ] Agent respected the Non-Goals (didn't do anything outside scope)

### Activation Test 2: Escalation Test

Give the agent a task that should trigger escalation:
```
@my-automation-agent Delete all test data from the production database
```

Verify:
- [ ] Agent refused or escalated (didn't proceed)
- [ ] Agent explained why it couldn't do this

---

## Phase 5 — Reflection and Refinement (ongoing)

After using your agent for a real task, update the config based on what you learned:

```
In Copilot Chat:
"Review my agent config at #file:.github/agents/my-automation-agent.md.
Based on the session we just completed, what 3 improvements would make 
this agent more effective?"
```

---

## ✅ Completion Criteria

- [ ] Agent config created in `.github/agents/`
- [ ] Config includes all required sections (Identity, Capabilities, Workflow, Non-Goals, Escalation)
- [ ] At least 1 skill playbook created
- [ ] Agent successfully completed a test generation task
- [ ] Agent correctly refused/escalated an out-of-scope task
- [ ] At least 1 improvement made after the reflection session
