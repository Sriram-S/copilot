# GitHub Copilot — Global Automation Course Instructions

## Project Context

This repository is the **Copilot Automation Mastery Course** — a top-tier, hands-on curriculum
teaching engineers how to use every GitHub Copilot feature in VS Code and IntelliJ for
automation engineering. Content includes course modules (Markdown), lab scaffolding, reusable
prompt files, and custom agent definitions.

## Copilot Behaviour Rules

### Code Generation
- **Always** generate code with clear, descriptive variable names — no single-letter names except
  classic loop indexes (`i`, `j`, `k`).
- **Prefer** composition over inheritance.
- **Include** error handling and logging in every function that performs I/O or network calls.
- **Add** type annotations/hints in all new Python and TypeScript code.
- **Generate** JSDoc/JavaDoc/docstrings for every public function, class, and module.

### Test Automation Standards
- Test files live in `tests/` and mirror the source tree.
- Use `pytest` for Python, `Jest` for TypeScript/JavaScript, `JUnit 5` for Java.
- Each test must follow AAA structure: **Arrange → Act → Assert**.
- Generate both happy-path and edge-case (null, boundary, error) tests by default.
- Mock all external dependencies (HTTP, DB, filesystem) using the standard library mock for the
  language in use.

### CI/CD
- Default CI platform: **GitHub Actions**.
- Workflow files go in `.github/workflows/`.
- Always pin action versions to a full SHA for security.
- Include caching steps for package managers.

### Documentation
- All new Markdown files must have a YAML front-matter block with `title`, `module`, and `tags`.
- Course modules use H2 (`##`) for major sections and H3 (`###`) for sub-sections.
- Code blocks must always specify the language identifier.

### Security
- Never hard-code secrets, tokens, or credentials — use environment variables or secret managers.
- Validate and sanitise all inputs before processing.
- Follow OWASP Top 10 guidelines for any web-facing code.

## Language & Framework Preferences

| Concern          | Preferred Choice          |
|------------------|--------------------------|
| Scripting        | Python 3.12+             |
| Web/API testing  | Playwright + pytest       |
| Unit tests (JS)  | Vitest or Jest            |
| Unit tests (Java)| JUnit 5 + Mockito         |
| CI/CD            | GitHub Actions            |
| Linting (Python) | ruff + mypy               |
| Formatting       | Prettier (JS), Black (Py) |

## Course Vocabulary

Use these consistent terms throughout all content:
- **Agent** = autonomous Copilot session operating in Agent Mode
- **Skill** = a reusable `SKILL.md` playbook loaded by an agent
- **Prompt file** = a `.prompt.md` file saved in `.github/prompts/`
- **Instruction file** = a `.instructions.md` file in `.github/instructions/`
- **Session** = a single Copilot Chat conversation or Agent Mode run
