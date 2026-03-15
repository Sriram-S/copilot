---
title: "Module 00 — Setup & Environment"
module: 0
tags: [setup, vscode, intellij, installation, configuration]
---

# Module 00 — Setup & Environment

## Learning Objectives

By the end of this module you will:
- Have a fully configured Copilot environment in VS Code **and** IntelliJ
- Understand the difference between Copilot Individual, Business, and Enterprise plans
- Know how to verify Copilot is working and how to diagnose problems
- Have settings configured for maximum productivity

---

## 0.1 Plan Tiers & What You Get

| Feature | Individual | Business | Enterprise |
|---------|-----------|----------|-----------|
| Inline completions | ✅ | ✅ | ✅ |
| Copilot Chat | ✅ | ✅ | ✅ |
| Agent Mode | ✅ | ✅ | ✅ |
| Custom Instructions | ✅ | ✅ | ✅ |
| Custom Agents | ✅ | ✅ | ✅ |
| Copilot in GitHub.com | ✅ | ✅ | ✅ |
| Policy controls | ❌ | ✅ | ✅ |
| Audit logs | ❌ | ✅ | ✅ |
| IP indemnity | ❌ | ✅ | ✅ |
| Copilot Knowledge Bases | ❌ | ❌ | ✅ |
| Fine-tuned models | ❌ | ❌ | ✅ |

> **Course note**: All exercises work with any plan. Enterprise-only features are clearly marked.

---

## 0.2 VS Code Setup

### Installation

1. Install [VS Code](https://code.visualstudio.com/) ≥ 1.95
2. Install the **GitHub Copilot** extension (`GitHub.copilot`)
3. Install the **GitHub Copilot Chat** extension (`GitHub.copilot-chat`)
4. Sign in: `Ctrl+Shift+P` → *GitHub Copilot: Sign In*

> As of VS Code 1.99, both extensions are bundled — you may only need one install.

### Verify Installation

Open the Command Palette (`Ctrl+Shift+P`) and run:
```
GitHub Copilot: Check Status
```

You should see: `✓ GitHub Copilot is enabled and authenticated.`

### Recommended VS Code Settings

Add these to your `settings.json` (`Ctrl+Shift+P` → *Open User Settings JSON*):

```json
{
  "github.copilot.enable": {
    "*": true,
    "plaintext": false,
    "markdown": true
  },
  "github.copilot.chat.agentMode": true,
  "github.copilot.chat.followUps": "always",
  "github.copilot.chat.experimental.generateTests": true,
  "github.copilot.nextEditSuggestions.enabled": true,
  "editor.inlineSuggest.enabled": true,
  "editor.inlineSuggest.showToolbar": "always"
}
```

### Essential Keyboard Shortcuts

| Action | Windows/Linux | macOS |
|--------|--------------|-------|
| Accept suggestion | `Tab` | `Tab` |
| Dismiss suggestion | `Esc` | `Esc` |
| Next suggestion | `Alt+]` | `Option+]` |
| Previous suggestion | `Alt+[` | `Option+[` |
| Accept word | `Ctrl+→` | `Cmd+→` |
| Open Copilot Chat | `Ctrl+Alt+I` | `Ctrl+Cmd+I` |
| Inline chat | `Ctrl+I` | `Cmd+I` |
| Open suggestions panel | `Ctrl+Enter` | `Ctrl+Enter` |

---

## 0.3 IntelliJ Setup

### Supported IDEs

The Copilot plugin works with all JetBrains IDEs on the same license:
- IntelliJ IDEA (Community & Ultimate)
- PyCharm, WebStorm, GoLand, Rider, CLion, DataGrip, etc.
- Minimum version: **2024.3**

### Installation

1. Open **Settings** → **Plugins** → **Marketplace**
2. Search for `GitHub Copilot`
3. Install and restart
4. Go to **Settings** → **GitHub Copilot** → **Sign In**

### IntelliJ Settings

```
Settings → Tools → GitHub Copilot
├── Enable completions: ✅
├── Show completions automatically: ✅
├── Enable Copilot Chat: ✅
└── Agent Mode (preview): ✅
```

### IntelliJ Keyboard Shortcuts

| Action | Windows/Linux | macOS |
|--------|--------------|-------|
| Accept suggestion | `Tab` | `Tab` |
| Dismiss | `Esc` | `Esc` |
| Next suggestion | `Alt+]` | `Option+]` |
| Previous suggestion | `Alt+[` | `Option+[` |
| Open Chat | `Alt+Shift+C` | `Option+Shift+C` |

---

## 0.4 Repository-Level Configuration

This course repo is pre-configured. In your own projects, create:

```
.github/
├── copilot-instructions.md        # Applied to every Copilot interaction
└── instructions/
    └── *.instructions.md          # Applied based on the applyTo glob pattern
```

Open `.github/copilot-instructions.md` in this repo to see the full example.

---

## 0.5 Quick Verification Exercise

After setup, open a new Python file and type the following comment, then wait for Copilot:

```python
# Function that returns the nth Fibonacci number using memoization
```

Copilot should generate a complete function. Accept it with `Tab`.

Then open Copilot Chat and type:
```
/tests Generate tests for the Fibonacci function I just wrote
```

If both work, your setup is complete. Move on to Module 01!

---

## Module Checkpoint

- [ ] VS Code or IntelliJ installed and updated
- [ ] Copilot extension installed and authenticated
- [ ] Settings JSON updated with recommended options
- [ ] Keyboard shortcuts memorised (print the cheat sheet!)
- [ ] Verification exercise completed successfully

**Next**: [Module 01 — Copilot Fundamentals](./MODULE-01-fundamentals.md)
