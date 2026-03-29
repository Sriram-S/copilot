# Module 02: VS Code Setup & Configuration for Test Automation

## Learning Objectives

- Install and configure GitHub Copilot extensions in VS Code
- Enable and tune the latest Copilot features (agent mode, parallel agents, edits mode)
- Configure workspace-level settings for a test automation project
- Understand every Copilot UI element in VS Code

---

## 2.1 Required Extensions

Install both extensions from the VS Code marketplace:

| Extension | ID | Purpose |
|---|---|---|
| GitHub Copilot | `GitHub.copilot` | Inline completions engine |
| GitHub Copilot Chat | `GitHub.copilot-chat` | Chat, agent mode, edits mode |

```bash
# Install via CLI
code --install-extension GitHub.copilot
code --install-extension GitHub.copilot-chat
```

After installation, sign in with your GitHub account via the Accounts menu (bottom-left avatar icon).

---

## 2.2 Key VS Code Settings for Test Automation

Add these to your workspace `.vscode/settings.json`:

```json
{
  // Copilot core settings
  "github.copilot.enable": {
    "*": true,
    "plaintext": false,
    "markdown": true,
    "yaml": true
  },
  "github.copilot.editor.enableAutoCompletions": true,

  // Agent mode (VS Code 1.99+)
  "chat.agent.enabled": true,

  // Edits mode
  "github.copilot.chat.edits.enabled": true,

  // Show completions in all test file types
  "editor.inlineSuggest.enabled": true,
  "editor.inlineSuggest.showToolbar": "always",

  // Copilot Next Edit Suggestions (NES) - predicts your next edit location
  "github.copilot.nextEditSuggestions.enabled": true,

  // Inline chat
  "inlineChat.mode": "live",

  // Test runner integration
  "testing.automaticallyOpenPeekView": "failureAnywhere"
}
```

---

## 2.3 The Copilot Interface in VS Code

### 2.3.1 Inline Completions

Ghost text appears as you type. Key interactions:

| Action | Keybinding |
|---|---|
| Accept suggestion | `Tab` |
| Dismiss suggestion | `Escape` |
| Next suggestion | `Alt+]` |
| Previous suggestion | `Alt+[` |
| Partial accept (word) | `Ctrl+Right` |
| Open completions panel | `Ctrl+Enter` |

### 2.3.2 Inline Chat (`Ctrl+I` / `Cmd+I`)

Open an AI chat directly on selected code. Perfect for:
- Explaining what a selector does
- Refactoring a Page Object method
- Adding assertions to an existing test

### 2.3.3 Copilot Chat Panel (`Ctrl+Alt+I`)

Full chat sidebar with:
- **`@workspace`** – queries entire codebase for context
- **`@vscode`** – queries VS Code settings/docs
- **`@terminal`** – queries terminal output
- **`#file`** – attach specific files as context
- **`#selection`** – attach current selection
- **`#codebase`** – deep semantic search across all files

### 2.3.4 Agent Mode

Switch to agent mode using the chat mode toggle (≡ icon in chat input). Agent mode can:
- Read and write multiple files autonomously
- Run terminal commands (with your approval)
- Install dependencies
- Execute test runners and analyze results

---

## 2.4 Enabling Experimental Features

Some features are behind feature flags. Enable in settings:

```json
{
  // Parallel agent tool calls (2025+)
  "github.copilot.chat.agent.thinkingTool": true,

  // Vision: attach screenshots to chat (analyze failing test screenshots!)
  "github.copilot.chat.visionEnabled": true,

  // Copilot commit message generation
  "github.copilot.chat.generateCommitMessageOnce": true,

  // Test generation shortcut
  "github.copilot.chat.testGeneration.instructions": []
}
```

---

## 2.5 Recommended Project Layout for Automation

```
my-automation-project/
├── .github/
│   ├── copilot-instructions.md      ← Global workspace instructions
│   ├── agents/
│   │   ├── test-generator.agent.md  ← Custom agent for test gen
│   │   └── reviewer.agent.md        ← Custom agent for review
│   ├── prompts/
│   │   ├── generate-page-object.prompt.md
│   │   ├── generate-api-tests.prompt.md
│   │   └── generate-test-data.prompt.md
│   └── instructions/
│       ├── playwright.instructions.md
│       └── java-selenium.instructions.md
├── .vscode/
│   └── settings.json
├── src/
│   └── test/
├── playwright.config.ts
└── README.md
```

---

## 2.6 VS Code Copilot Chat Participants (Slash Commands)

Inside the chat panel, slash commands route your request:

| Command | What it does |
|---|---|
| `/explain` | Explains selected code |
| `/fix` | Suggests a fix for an error |
| `/tests` | Generates tests for selected code |
| `/doc` | Generates documentation |
| `/simplify` | Simplifies complex code |
| `/new` | Creates a new file/project |
| `/newNotebook` | Creates a Jupyter notebook |

**Automation-specific tip:** Select a function in your app code and run `/tests` to instantly generate test cases.

---

## 2.7 Context Variables for Test Automation

When writing prompts in Copilot Chat, these context variables are critical:

```
#file:playwright.config.ts   ← Include config in context
#file:src/pages/LoginPage.ts ← Include Page Object in context
#selection                    ← Include your current selection
#codebase                    ← Semantic search across whole project
```

**Example prompt:**
```
Using #file:src/pages/LoginPage.ts as the Page Object, generate 
Playwright tests for all the methods defined in LoginPage. Include 
positive and negative test cases. Follow the pattern in #file:src/tests/signup.spec.ts
```

---

## Knowledge Check

1. What setting enables agent mode in VS Code?
2. Which context variable performs a semantic search across all files in a project?
3. What is "Next Edit Suggestions" and how does it help a tester?

---

## Next Module

➡️ [Module 03: IntelliJ Setup & Configuration](../module-03/README.md)
