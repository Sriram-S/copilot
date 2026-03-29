# Module 03: IntelliJ IDEA Setup & Configuration for Test Automation

## Learning Objectives

- Install and configure the GitHub Copilot plugin in IntelliJ IDEA
- Leverage IntelliJ-specific Copilot features for Java/Kotlin automation
- Configure Copilot for Selenium, Appium, RestAssured, and TestNG/JUnit projects
- Use Copilot Chat within IntelliJ's tool windows

---

## 3.1 Installing the GitHub Copilot Plugin

### Method 1: From the Marketplace
1. Open IntelliJ IDEA
2. Go to **Settings** → **Plugins** → **Marketplace**
3. Search for "GitHub Copilot"
4. Click **Install** and restart

### Method 2: From the command line
```bash
# Download and install the plugin via JetBrains CLI tool
# Or use the IDE's plugin manager with the plugin ID:
# Plugin ID: com.github.copilot
```

### Sign In
After installation, click the Copilot icon in the status bar (bottom-right) and select **Sign In to GitHub**.

---

## 3.2 IntelliJ Copilot Feature Overview

| Feature | IntelliJ Support | Notes |
|---|---|---|
| Inline completions | ✅ Full | Works in all file types |
| Copilot Chat | ✅ Full | Dedicated tool window |
| Agent mode | ✅ (2024.3+) | Autonomous task execution |
| `/tests` slash command | ✅ Full | Generates JUnit/TestNG tests |
| `/explain` slash command | ✅ Full | Explains complex code |
| `/fix` slash command | ✅ Full | Suggests fixes for errors |
| Context: `#file` | ✅ Full | Attach files to context |
| Context: `@workspace` | ✅ Full | Project-wide context |
| Custom instructions | ✅ Full | Via `.github/copilot-instructions.md` |

---

## 3.3 Key Keybindings in IntelliJ

| Action | Windows/Linux | macOS |
|---|---|---|
| Accept completion | `Tab` | `Tab` |
| Dismiss completion | `Escape` | `Escape` |
| Next completion | `Alt+]` | `Option+]` |
| Previous completion | `Alt+[` | `Option+[` |
| Open Copilot Chat | `Ctrl+Shift+G` | `Cmd+Shift+G` |
| Inline chat | Select code → Right-click → Copilot | Same |
| Generate tests | `Alt+Enter` → "Generate tests with Copilot" | `Option+Enter` |

---

## 3.4 IntelliJ Copilot Chat Tool Window

The Chat tool window appears on the right side. Key features:

### Chat Participants
- **`@workspace`** – Searches across all project files
- **`#file`** – Attach a specific file as context

### Slash Commands for Java Test Automation

```
/tests    → Generate JUnit 5 or TestNG test class
/fix      → Fix compilation errors or test failures
/explain  → Explain what a Selenium selector does
/doc      → Generate Javadoc for a method
```

### Example: Generate RestAssured Tests

```
Using @workspace context, generate RestAssured tests for the 
UserController endpoints. Include:
- GET /api/users (list all users)
- POST /api/users (create user with validation)
- DELETE /api/users/{id} (delete with auth)
Use TestNG annotations and the existing BaseTest class pattern.
```

---

## 3.5 IntelliJ-Specific Copilot Features

### 3.5.1 Test Generation from Alt+Enter

When your cursor is on a class or method:
1. Press `Alt+Enter` (macOS: `Option+Enter`)
2. Select **"Generate Tests with Copilot"**
3. Copilot will create a new test class with suggested test cases

### 3.5.2 Error Explanation in the Problems Panel

When there's a compilation or runtime error:
1. Hover over the red underline
2. Click the Copilot icon in the tooltip
3. Select **"Explain with Copilot"** or **"Fix with Copilot"**

### 3.5.3 Commit Message Generation

1. Open the Git commit dialog
2. Click the Copilot sparkle icon (✨)
3. Copilot generates a conventional commit message based on your diff

---

## 3.6 Configuration for Java Test Automation Projects

### Maven Project Setup (pom.xml additions)

```xml
<!-- Copilot works best when it sees your test dependencies -->
<dependencies>
    <!-- Selenium WebDriver -->
    <dependency>
        <groupId>org.seleniumhq.selenium</groupId>
        <artifactId>selenium-java</artifactId>
        <version>4.20.0</version>
    </dependency>
    <!-- TestNG -->
    <dependency>
        <groupId>org.testng</groupId>
        <artifactId>testng</artifactId>
        <version>7.10.1</version>
        <scope>test</scope>
    </dependency>
    <!-- RestAssured -->
    <dependency>
        <groupId>io.rest-assured</groupId>
        <artifactId>rest-assured</artifactId>
        <version>5.4.0</version>
        <scope>test</scope>
    </dependency>
    <!-- AssertJ -->
    <dependency>
        <groupId>org.assertj</groupId>
        <artifactId>assertj-core</artifactId>
        <version>3.25.3</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

Having these dependencies in your `pom.xml` gives Copilot context about which APIs are available.

---

## 3.7 IntelliJ Copilot for Appium Mobile Testing

Copilot understands Appium's Java client API. Example prompts:

```
Generate an Appium test class for Android that:
1. Sets up the driver with Android capabilities for a local emulator
2. Opens the 'Settings' app
3. Navigates to 'Network & internet'
4. Asserts that WiFi toggle is visible
Use TestNG @BeforeMethod/@AfterMethod lifecycle.
```

```
Refactor this XPath locator to use the more stable 
UiAutomator2 strategy: //android.widget.TextView[@text='WiFi']
```

---

## 3.8 Setting Up `.github/copilot-instructions.md` for IntelliJ

IntelliJ reads the same workspace instructions file as VS Code. Example for Java teams:

```markdown
# Copilot Workspace Instructions

## Project Context
This is a Java 17 test automation framework using:
- Selenium 4.x with WebDriver Manager
- TestNG 7.x
- RestAssured 5.x
- Allure for reporting
- Maven for build management

## Code Standards
- All Page Objects must extend BasePage
- Tests must extend BaseTest which handles WebDriver lifecycle
- Use @DataProvider for data-driven tests, never hardcode test data
- All assertions must use AssertJ (not JUnit/TestNG built-ins)
- Follow Page Factory pattern for element initialization

## Test Naming Convention
- Unit tests: `should[Action][Context]`
- Integration tests: `verify[Feature][Scenario]`
- E2E tests: `e2e_[UserJourney]_[Outcome]`
```

---

## Knowledge Check

1. How do you trigger test generation in IntelliJ without opening the Chat panel?
2. What does `@workspace` do differently from `#file`?
3. Why is having the correct dependencies in `pom.xml` important for Copilot quality?

---

## Next Module

➡️ [Module 04: Inline Completions Mastery](../module-04/README.md)
