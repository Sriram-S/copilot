# Copilot Workspace Instructions

## About This Repository

This repository is a **Copilot Automation Mastery Course** — a comprehensive learning resource for test automation engineers who want to leverage GitHub Copilot in VS Code and IntelliJ IDEA.

## Repository Structure

```
course/module-01/ through module-14/  ← Theory modules with examples
labs/lab-01/ through lab-09/          ← Hands-on labs with step-by-step instructions
.github/agents/                       ← Custom agent definitions (.agent.md)
.github/prompts/                      ← Reusable prompt templates (.prompt.md)
.github/instructions/                 ← Scoped instruction files (.instructions.md)
```

## When Generating Content for This Repository

### Markdown Files (Course Modules and Labs)

All course content is in Markdown. When generating or editing:

1. **Use proper Markdown heading hierarchy** — H1 for module title, H2 for major sections, H3 for subsections
2. **Use fenced code blocks** with language tags for all code examples: `typescript`, `java`, `bash`, `json`, `yaml`, `markdown`
3. **Use tables** for comparisons, keybindings, and feature matrices
4. **Use numbered lists** for sequential steps, bullet points for non-sequential items
5. **Add a Knowledge Check section** at the end of each course module
6. **Add a Next Module/Lab link** at the end of each file
7. **Module files live at**: `course/module-NN/README.md`
8. **Lab files live at**: `labs/lab-NN/README.md`

### Code Examples in Course Content

All code examples in course modules must be:
- **Runnable**: Code should work as-is or with minimal setup
- **Commented**: Include inline comments explaining key points
- **Consistent**: Use the same framework/style within a module
- **Modern**: Use latest stable APIs (Playwright 1.44+, TypeScript 5.x, Java 17+)

**Default code example stack (unless otherwise specified):**
- UI tests: Playwright TypeScript
- API tests: Supertest + Jest + TypeScript
- Java tests: Selenium 4 + TestNG + AssertJ + Allure
- CI/CD: GitHub Actions

### Agent Files (`.github/agents/*.agent.md`)

When creating or updating agent files:
- Use YAML frontmatter with: `name`, `description`, `tools`
- Tools must be from: `read_file`, `write_file`, `run_in_terminal`, `search_workspace`, `fetch_webpage`
- Include clear step-by-step instructions for the agent
- Include quality standards the agent should enforce
- Include output format specification
- Keep agents focused on a single concern (test generation, review, debugging, etc.)

### Prompt Files (`.github/prompts/*.prompt.md`)

When creating or updating prompt files:
- Use YAML frontmatter with: `mode` (ask/edit/agent), `description`
- Use `[PLACEHOLDER]` for variables the user fills in
- Include requirements section, code examples, and output specification
- End with: what files are created and how to run them

### Instruction Files (`.github/instructions/*.instructions.md`)

When creating or updating instruction files:
- Use YAML frontmatter with: `applyTo` (glob pattern)
- Be specific and actionable — "do X" not "consider doing X"
- Include both ✅ correct and ❌ incorrect code examples
- Keep each instruction file focused on ONE concern (test files OR page objects, not both)

## Writing Style for Course Content

- **Audience**: Mid-level test automation engineers familiar with at least one framework
- **Tone**: Direct, practical, no fluff — engineers want to learn by doing
- **Examples**: Always include real, runnable code examples, not pseudocode
- **Exercises**: Each module should end with at least 2-3 hands-on exercises
- **Depth**: Cover "what" and "how" but also "why" — explain the reasoning behind recommendations

## Automation-Specific Content Rules

When generating test code examples for the course:
- Always show BOTH the approach and the resulting output/behavior
- Include common failure modes and how to handle them
- Show VS Code keybindings AND IntelliJ equivalents for dual-IDE coverage
- Tag examples with the relevant module number: `// Module 07: Parallel Agents`

## What NOT to Generate in This Repository

- Don't generate actual application source code (only test code)
- Don't generate framework implementations (only usage examples)
- Don't modify `.git/` directory contents
- Don't create temporary or scratch files at the root level
