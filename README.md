# Copilot Automation Mastery Course

> **The definitive end-to-end guide to using GitHub Copilot for test automation in VS Code and IntelliJ IDEA**

[![GitHub Copilot](https://img.shields.io/badge/GitHub%20Copilot-Powered-blue?logo=github)](https://github.com/features/copilot)
[![VS Code](https://img.shields.io/badge/VS%20Code-1.99+-blue?logo=visual-studio-code)](https://code.visualstudio.com/docs/copilot/overview)
[![IntelliJ](https://img.shields.io/badge/IntelliJ%20IDEA-2024.1+-orange?logo=intellij-idea)](https://plugins.jetbrains.com/plugin/17718-github-copilot)
[![Playwright](https://img.shields.io/badge/Playwright-1.44+-green?logo=playwright)](https://playwright.dev)
[![Selenium](https://img.shields.io/badge/Selenium-4.x-green?logo=selenium)](https://selenium.dev)

---

## What You'll Learn

This course takes you from Copilot basics to orchestrating autonomous parallel agents that scaffold, generate, debug, and maintain complete test automation suites. Every module covers both **VS Code** and **IntelliJ IDEA**.

### Key Skills

| Skill | Module |
|---|---|
| Inline completions mastery | 04 |
| Chat + `@workspace` + Vision | 05 |
| Agent mode (autonomous scaffolding) | 06 |
| Parallel agents for large suites | 07 |
| Custom instructions (zero-repetition prompting) | 08 |
| Prompt engineering for testers | 09 |
| Test generation from source, specs, stories | 10 |
| Full E2E lifecycle automation | 11 |
| CI/CD generation and debugging | 12 |
| Edits mode, MCP, thinking tool | 13 |
| Team integration and best practices | 14 |

---

## Course Structure

### 📚 Modules

| # | Module | Key Topics |
|---|---|---|
| [01](course/module-01/README.md) | Introduction & Overview | What Copilot is, why automation, course roadmap |
| [02](course/module-02/README.md) | VS Code Setup & Configuration | Extensions, settings, UI guide, agent mode enablement |
| [03](course/module-03/README.md) | IntelliJ Setup & Configuration | Plugin, keybindings, Java-specific features |
| [04](course/module-04/README.md) | Inline Completions Mastery | Comments-driven generation, NES, partial accept |
| [05](course/module-05/README.md) | Chat Interface Deep Dive | `@workspace`, `#file`, Vision, slash commands |
| [06](course/module-06/README.md) | Agent Mode & Custom Agents | `.agent.md` files, autonomous scaffolding, MCP |
| [07](course/module-07/README.md) | Parallel Agents & Orchestration | Parallel workstreams, orchestrator patterns |
| [08](course/module-08/README.md) | Custom Instructions | `copilot-instructions.md`, scoped `.instructions.md` |
| [09](course/module-09/README.md) | Prompt Engineering for Testers | Anatomy of a prompt, `.prompt.md`, few-shot, CoT |
| [10](course/module-10/README.md) | AI-Powered Test Generation | From source, specs, OpenAPI, user stories, schemas |
| [11](course/module-11/README.md) | End-to-End Automation | Full lifecycle: scaffold → develop → debug → maintain |
| [12](course/module-12/README.md) | CI/CD Integration | GitHub Actions, Docker, parallel sharding, PR comments |
| [13](course/module-13/README.md) | Advanced & Latest Features | Edits mode, Vision, MCP servers, thinking tool |
| [14](course/module-14/README.md) | Best Practices & Team Patterns | 10 commandments, metrics, scaling across teams |

### 🧪 Labs (Hands-On)

| # | Lab | Duration | Framework |
|---|---|---|---|
| [01](labs/lab-01/README.md) | Your First Copilot-Generated Test | 30 min | TypeScript + Jest |
| [02](labs/lab-02/README.md) | Page Object Model Generation | 45 min | Playwright TS |
| [03](labs/lab-03/README.md) | API Test Generation from OpenAPI | 45 min | Jest + Supertest |
| [04](labs/lab-04/README.md) | Agent Mode — Full Project Scaffold | 60 min | Playwright TS |
| [05](labs/lab-05/README.md) | Custom Agents & Prompt Files | 45 min | VS Code |
| [06](labs/lab-06/README.md) | Custom Instructions & Workspace Config | 30 min | Any |
| [07](labs/lab-07/README.md) | Debugging & Flaky Test Analysis | 60 min | Playwright TS |
| [08](labs/lab-08/README.md) | Full E2E Project — New Feature Coverage | 90 min | Playwright TS |
| [09](labs/lab-09/README.md) | Capstone — Java Selenium Suite in IntelliJ | 90 min | Selenium Java |

---

## Repository Artifacts

### 🤖 Custom Agents (`.github/agents/`)

| Agent | Description |
|---|---|
| [`test-generator.agent.md`](.github/agents/test-generator.agent.md) | Generates comprehensive test suites following project conventions |
| [`reviewer.agent.md`](.github/agents/reviewer.agent.md) | Reviews tests for quality, coverage, and flakiness |
| [`orchestrator.agent.md`](.github/agents/orchestrator.agent.md) | Orchestrates large test generation across parallel workstreams |
| [`flaky-test-analyst.agent.md`](.github/agents/flaky-test-analyst.agent.md) | Diagnoses and fixes flaky tests |

### 📄 Reusable Prompts (`.github/prompts/`)

| Prompt | Description |
|---|---|
| [`generate-page-object.prompt.md`](.github/prompts/generate-page-object.prompt.md) | Generate a Playwright Page Object |
| [`generate-api-tests.prompt.md`](.github/prompts/generate-api-tests.prompt.md) | Generate comprehensive API test suite |
| [`generate-test-data.prompt.md`](.github/prompts/generate-test-data.prompt.md) | Add TestDataFactory entries |
| [`analyze-flakiness.prompt.md`](.github/prompts/analyze-flakiness.prompt.md) | Diagnose and fix flaky tests |
| [`coverage-gap-analysis.prompt.md`](.github/prompts/coverage-gap-analysis.prompt.md) | Find and fill coverage gaps |
| [`generate-ci-workflow.prompt.md`](.github/prompts/generate-ci-workflow.prompt.md) | Generate GitHub Actions CI workflow |
| [`review-test-quality.prompt.md`](.github/prompts/review-test-quality.prompt.md) | Code review test quality |
| [`generate-accessibility-tests.prompt.md`](.github/prompts/generate-accessibility-tests.prompt.md) | Generate WCAG a11y tests |

### 📋 Custom Instructions (`.github/instructions/`)

| File | Applies To |
|---|---|
| [`playwright-tests.instructions.md`](.github/instructions/playwright-tests.instructions.md) | `tests/**/*.spec.ts` |
| [`page-objects.instructions.md`](.github/instructions/page-objects.instructions.md) | `src/pages/**/*.ts` |
| [`java-selenium-tests.instructions.md`](.github/instructions/java-selenium-tests.instructions.md) | `src/test/java/**/*.java` |

---

## Quick Start

### Prerequisites

- GitHub account with [Copilot subscription](https://github.com/features/copilot)
- VS Code 1.90+ **and/or** IntelliJ IDEA 2024.1+
- Node.js 18+ (for TypeScript labs)
- Java 17+ (for Java labs)

### Install Copilot Extensions

**VS Code:**
```bash
code --install-extension GitHub.copilot
code --install-extension GitHub.copilot-chat
```

**IntelliJ IDEA:**
> Settings → Plugins → Marketplace → Search "GitHub Copilot" → Install

### Enable Agent Mode (VS Code)

Add to `.vscode/settings.json`:
```json
{
  "chat.agent.enabled": true,
  "github.copilot.nextEditSuggestions.enabled": true
}
```

### Start the Course

1. Read [Module 01: Introduction](course/module-01/README.md)
2. Do [Lab 01: First Generated Test](labs/lab-01/README.md)
3. Continue through modules and labs in order

---

## Reference: Essential VS Code Copilot Keybindings

| Action | Windows/Linux | macOS |
|---|---|---|
| Accept inline suggestion | `Tab` | `Tab` |
| Next suggestion | `Alt+]` | `Option+]` |
| Open Copilot Chat | `Ctrl+Alt+I` | `Cmd+Option+I` |
| Inline chat | `Ctrl+I` | `Cmd+I` |
| Open completions panel | `Ctrl+Enter` | `Cmd+Enter` |

## Reference: Essential Copilot Chat Commands

| Command | Purpose |
|---|---|
| `/tests` | Generate tests for selected code |
| `/explain` | Explain selected code |
| `/fix` | Fix an error in selected code |
| `/doc` | Generate documentation |
| `@workspace` | Search entire project |
| `#file:path` | Attach specific file as context |
| `#codebase` | Deep semantic search |

---

## External Resources

- 📖 [VS Code Copilot Documentation](https://code.visualstudio.com/docs/copilot/overview)
- 📖 [GitHub Copilot Changelog](https://github.blog/changelog/)
- 📖 [Playwright Documentation](https://playwright.dev/docs/intro)
- 📖 [GitHub Copilot Chat Participants](https://code.visualstudio.com/docs/copilot/copilot-chat)
- 📖 [Model Context Protocol](https://modelcontextprotocol.io/)
- 📖 [GitHub Copilot Extensions](https://github.com/features/copilot/extensions)
- 📖 [IntelliJ Copilot Plugin](https://plugins.jetbrains.com/plugin/17718-github-copilot)

---

## Contributing

This course is actively maintained. If you find:
- Outdated information (Copilot features change fast!)
- Better examples or prompts
- New features to cover

Please open an issue or PR. The community grows this resource.

---

*Built with 💙 for the test automation community. Powered by GitHub Copilot.*