---
title: "Module 06 — Custom Agents & Skills"
module: 6
tags: [custom-agents, skills, agent-config, playbooks, github-agents]
---

# Module 06 — Custom Agents & Skills

## Learning Objectives

- Create fully configured custom agents in `.github/agents/`
- Define agent skills as reusable playbooks (`SKILL.md` files)
- Assign permissions and tools to each agent
- Chain agents for multi-role automation workflows
- Share and version agent configurations as code

---

## 6.1 What Is a Custom Agent?

A **custom agent** is a pre-configured Copilot assistant with:
- A specific **persona and domain expertise**
- A defined set of **tools and permissions**
- Pre-loaded **skills** (reusable step playbooks)
- **Context** about your project automatically applied

Think of it as hiring a specialist contractor who already knows your codebase conventions.

---

## 6.2 Agent Configuration — File Structure

```
.github/
└── agents/
    ├── automation-specialist.md    # General automation agent
    ├── test-engineer.md            # Test-focused agent
    ├── devops-engineer.md          # CI/CD and infrastructure agent
    └── security-reviewer.md        # Security audit agent
```

---

## 6.3 Agent Configuration File Format

```markdown
# <Agent Name>

## Identity
Description of the agent's role and expertise.

## Capabilities
- Bullet list of what this agent can do
- Be specific about languages, frameworks, domains

## Workflow
Step-by-step procedure the agent follows for tasks.

## Tools
List of tools the agent is allowed to use.

## Coding Standards
Link to or repeat the key standards for this agent's domain.

## Escalation Rules
When the agent should stop and ask a human.

## Skills Loaded
List of skill playbooks this agent has access to.
```

---

## 6.4 Creating a Security Reviewer Agent

```markdown
# Security Reviewer Agent

## Identity
You are **SecureBot**, a senior application security engineer with expertise in:
- OWASP Top 10
- Secure coding practices (input validation, output encoding, crypto)
- Dependency vulnerability management
- Infrastructure-as-Code security

## Workflow
1. Read all changed files in the PR/commit
2. Identify security concerns by category (injection, auth, crypto, secrets, etc.)
3. For each concern, rate severity: CRITICAL / HIGH / MEDIUM / LOW / INFO
4. Propose a specific code fix for each finding
5. Implement fixes for LOW and MEDIUM issues automatically
6. Report CRITICAL and HIGH for human review with full explanation

## Non-Goals
- Do NOT refactor code for style or performance
- Do NOT change business logic
- Do NOT approve/merge PRs — only report and fix

## Output Format
\`\`\`markdown
## Security Review Report

**Date**: <today>
**Files Reviewed**: <list>

### CRITICAL (requires immediate human action)
- [ ] ...

### HIGH
- [ ] ...

### Fixes Applied Automatically
- [x] ...
\`\`\`
```

---

## 6.5 Agent Skills — `SKILL.md` Playbooks

**Skills** are structured step-by-step playbooks that an agent loads when a task matches.

**Location**: `.github/agents/skills/` or as a reference in the agent config.

### Example Skill: Generate API Test Suite

```markdown
# Skill: Generate API Test Suite

## Trigger
When asked to "test an API endpoint" or "generate API tests"

## Steps

### Step 1: Discover the Endpoint
- Read the router/controller file
- Extract: HTTP method, path, path params, query params, request body schema, response schema

### Step 2: Identify Test Scenarios
- Happy path (valid request → expected response code and body)
- Missing required field (→ 422)
- Invalid type (→ 422)
- Unauthorised (→ 401 if auth required)
- Forbidden (→ 403 if RBAC applies)
- Not found (→ 404 for ID-based endpoints)
- Conflict (→ 409 for duplicate creation)
- Boundary values for numeric/string fields

### Step 3: Generate the Test File
- Use pytest + httpx.AsyncClient
- One test function per scenario
- Arrange-Act-Assert structure
- Parametrize where multiple inputs share the same assertion

### Step 4: Run and Verify
- Execute: `pytest tests/api/ -v --tb=short`
- Fix any failing tests
- Report coverage delta

### Output
- Test file at `tests/api/test_<resource>.py`
- Coverage report snippet
```

---

## 6.6 Using Custom Agents in VS Code

### Start a Custom Agent Session

1. Open Copilot Chat
2. In the model/agent selector, choose your custom agent by name
3. Type your task — the agent will apply its persona, skills, and constraints automatically

### Via Chat Reference

```
@automation-specialist Generate tests for the payment service
@test-engineer Review the test coverage for the auth module
@devops-engineer Add a security scan step to the CI pipeline
```

---

## 6.7 Multi-Agent Workflows

Chain agents for complex tasks:

```
┌─────────────────────────────────────────────────────────┐
│  TASK: "Implement and ship the new payment feature"      │
└──────────────┬──────────────────────────────────────────┘
               │
               ▼
  ┌─────────────────────────┐
  │  @automation-specialist  │  → Implements the feature code
  │  "Write the payment      │
  │   service module"        │
  └──────────┬──────────────┘
             │ hands off to
             ▼
  ┌─────────────────────────┐
  │  @test-engineer          │  → Generates full test suite
  │  "Generate tests for     │
  │   PaymentService"        │
  └──────────┬──────────────┘
             │ hands off to
             ▼
  ┌─────────────────────────┐
  │  @security-reviewer      │  → Audits code for vulnerabilities
  │  "Review the payment     │
  │   module for security"   │
  └──────────┬──────────────┘
             │ hands off to
             ▼
  ┌─────────────────────────┐
  │  @devops-engineer        │  → Updates CI to include new tests
  │  "Update pipeline for    │
  │   payment service tests" │
  └─────────────────────────┘
```

---

## 6.8 Versioning and Sharing Agent Configs

Agent configuration files are **plain Markdown committed to the repo** — they are:
- Version-controlled with the codebase
- Reviewed via normal PR process
- Shared with the entire team automatically
- Evolvable as your standards mature

**Team workflow**: Treat agent `.md` files like infrastructure-as-code. Review them in PRs.

---

## Module Checkpoint

- [ ] Created a custom `automation-specialist.md` agent in `.github/agents/`
- [ ] Created a `test-engineer.md` agent with a defined escalation policy
- [ ] Wrote at least one `SKILL.md` playbook
- [ ] Invoked a custom agent from Copilot Chat using `@agent-name`
- [ ] Designed a multi-agent handoff workflow for a real team scenario

**Next**: [Module 07 — Test Automation with Copilot](./MODULE-07-test-automation.md)
