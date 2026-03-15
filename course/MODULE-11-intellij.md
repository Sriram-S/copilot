---
title: "Module 11 — IntelliJ Deep Dive"
module: 11
tags: [intellij, jetbrains, java, kotlin, spring, gradle, maven]
---

# Module 11 — IntelliJ Deep Dive

## Learning Objectives

- Use every Copilot feature specific to JetBrains IDEs
- Leverage IntelliJ's refactoring tools alongside Copilot
- Generate Spring Boot application code and tests
- Use Copilot with Java/Kotlin idioms
- Integrate Copilot into the IntelliJ debugging workflow

---

## 11.1 IntelliJ-Specific Copilot Features

| Feature | VS Code | IntelliJ |
|---------|---------|---------|
| Inline completions | ✅ | ✅ |
| Copilot Chat | ✅ | ✅ |
| Agent Mode | ✅ | ✅ (since 2024.3+) |
| Custom Instructions | ✅ | ✅ |
| `/explain` | ✅ | ✅ |
| `/fix` | ✅ | ✅ |
| `/tests` | ✅ | ✅ |
| App modernisation | ❌ | ✅ (unique feature) |
| IntelliJ refactoring context | ❌ | ✅ |
| Build output context | ❌ | ✅ |
| JVM-aware suggestions | partial | ✅ Full |

---

## 11.2 Chat Panel in IntelliJ

Open the Copilot Chat panel:
- **Menu**: Tools → GitHub Copilot → Open Chat
- **Shortcut**: `Alt+Shift+C` (Windows/Linux) or `Option+Shift+C` (macOS)

The panel docks to the right side. You can:
- Drag and drop files from the Project tree into Chat for context
- Right-click a method → "Ask Copilot" to open an inline chat for that symbol
- Use "Explain" from the right-click context menu

---

## 11.3 IntelliJ Context Menu Integrations

Right-click on any code to access:

| Menu Item | What it does |
|-----------|-------------|
| Ask Copilot | Open inline chat for selected code |
| Explain with Copilot | Get a plain-English explanation |
| Fix with Copilot | Ask Copilot to fix compilation/inspection errors |
| Generate Tests | Generate JUnit 5 tests for the selected class/method |
| Refactor with Copilot | Suggest refactoring options |
| Document with Copilot | Generate Javadoc |

---

## 11.4 Spring Boot — Copilot-Accelerated Development

### Generate a REST Controller

```java
/**
 * REST controller for managing user accounts.
 * Endpoints:
 * - GET /api/v1/users/{id} - Get user by ID
 * - POST /api/v1/users - Create a new user
 * - PUT /api/v1/users/{id} - Update user
 * - DELETE /api/v1/users/{id} - Soft delete user
 * - GET /api/v1/users?page=0&size=20 - List users (paginated)
 *
 * Uses: UserService for business logic, UserMapper for DTO conversion.
 * Auth: All endpoints require ROLE_USER. DELETE requires ROLE_ADMIN.
 */
@RestController
@RequestMapping("/api/v1/users")
public class UserController {
    // Copilot generates the full implementation
```

### Generate JUnit 5 + Mockito Tests

```java
// In Chat:
// /tests Generate JUnit 5 tests for UserController using @WebMvcTest.
// Mock UserService with Mockito. Cover:
// - GET by ID: found (200), not found (404), invalid UUID (400)
// - POST: valid body (201), validation failure (400), duplicate email (409)
// - DELETE: success (204), unauthorized non-admin (403), not found (404)
// Use MockMvc for HTTP assertions and verify service method calls.
```

### Generated Test Pattern (Java)

```java
@WebMvcTest(UserController.class)
@AutoConfigureMockMvc
class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private UserService userService;

    @Test
    @WithMockUser
    void getUser_returnsOk_whenUserExists() throws Exception {
        // Arrange
        UUID userId = UUID.randomUUID();
        UserDTO expected = new UserDTO(userId, "Alice", "alice@example.com");
        when(userService.getUserById(userId)).thenReturn(Optional.of(expected));

        // Act & Assert
        mockMvc.perform(get("/api/v1/users/{id}", userId))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.name").value("Alice"))
            .andExpect(jsonPath("$.email").value("alice@example.com"));

        verify(userService).getUserById(userId);
    }

    @Test
    @WithMockUser
    void getUser_returnsNotFound_whenUserDoesNotExist() throws Exception {
        // Arrange
        UUID userId = UUID.randomUUID();
        when(userService.getUserById(userId)).thenReturn(Optional.empty());

        // Act & Assert
        mockMvc.perform(get("/api/v1/users/{id}", userId))
            .andExpect(status().isNotFound());
    }
}
```

---

## 11.5 Kotlin — Copilot Idioms

Copilot understands Kotlin idioms deeply. Leverage this:

```kotlin
// Prompt: "Implement a Kotlin extension function on List<Order> 
// that returns the total value grouped by customer ID"

// Copilot generates:
fun List<Order>.totalByCustomer(): Map<String, BigDecimal> =
    groupBy { it.customerId }
        .mapValues { (_, orders) ->
            orders.sumOf { it.total }
        }
```

```kotlin
// Prompt: "Generate a Kotlin sealed class hierarchy for representing 
// API call results: Success with data, Error with message and code,
// Loading state"

// Copilot generates:
sealed class ApiResult<out T> {
    data class Success<T>(val data: T) : ApiResult<T>()
    data class Error(val message: String, val code: Int) : ApiResult<Nothing>()
    data object Loading : ApiResult<Nothing>()
}
```

---

## 11.6 App Modernisation (IntelliJ Only)

IntelliJ has a unique **App Modernisation** feature powered by Copilot. It can:
- Migrate legacy Java/Spring code to modern standards
- Upgrade deprecated APIs (e.g., JUnit 4 → JUnit 5)
- Convert Java to Kotlin
- Modernise XML-based Spring config to annotation-based

### How to Use

```
Copilot Chat (IntelliJ):
"Use app modernisation to upgrade this project from JUnit 4 to JUnit 5.
Migrate all test classes in src/test/java/."
```

Or via the menu: **Tools → GitHub Copilot → Modernise Application**

---

## 11.7 Using IntelliJ Debugger with Copilot

When paused at a breakpoint:

1. Copy the stack trace and variable values from the Debugger panel
2. Paste into Copilot Chat:
   ```
   I'm paused at this breakpoint. Variables: [paste]
   Stack trace: [paste]
   
   Why is userList null here, and what is the correct fix?
   ```

IntelliJ 2025.1+ allows Copilot to **read the debugger state directly** via the `@debugger`
participant (similar to `@terminal` in VS Code).

---

## 11.8 Gradle / Maven Build Integration

When a build fails, use Copilot Chat with the build output:

```
This Maven build is failing:
[paste mvn output]

1. What is causing the failure?
2. Which pom.xml change would fix it?
3. Is this a dependency conflict? If so, show me the dependency tree command.
```

Or run directly: `mvn dependency:tree | copilot-chat` (paste output into Chat).

---

## 11.9 IntelliJ Keyboard Shortcuts (Full Reference)

| Action | Windows/Linux | macOS |
|--------|--------------|-------|
| Accept suggestion | `Tab` | `Tab` |
| Dismiss suggestion | `Esc` | `Esc` |
| Next suggestion | `Alt+]` | `Option+]` |
| Previous suggestion | `Alt+[` | `Option+[` |
| Accept word | `Ctrl+→` | `Cmd+→` |
| Open Copilot Chat | `Alt+Shift+C` | `Option+Shift+C` |
| Ask Copilot (selection) | Right-click → Ask Copilot | Same |
| Explain with Copilot | Right-click → Explain | Same |
| Generate Tests | Right-click → Generate Tests | Same |
| Fix with Copilot | `Alt+Enter` → Fix with Copilot | `Option+Enter` |

---

## Module Checkpoint

- [ ] Installed and configured the Copilot plugin in IntelliJ
- [ ] Generated a complete Spring Boot REST controller with Copilot
- [ ] Generated JUnit 5 + Mockito tests using `/tests` in IntelliJ Chat
- [ ] Used the right-click context menu for Explain, Fix, and Document
- [ ] Tried the App Modernisation feature on a legacy class
- [ ] Used Copilot with IntelliJ's debugger to diagnose a bug

**Next**: [Module 12 — Advanced Combinations](./MODULE-12-advanced-combinations.md)
