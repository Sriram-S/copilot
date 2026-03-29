# Module 04: Inline Completions Mastery for Test Automation

## Learning Objectives

- Master inline completion acceptance patterns for maximum velocity
- Learn how to guide Copilot with strategic comments and naming
- Understand how Copilot generates entire test functions from minimal context
- Use Next Edit Suggestions to navigate predicted change locations

---

## 4.1 How Inline Completions Work

Copilot's inline completions are triggered by your cursor position and surrounding context. The model considers:

1. **File context** – Current file content (language, imports, existing patterns)
2. **Open tabs** – Other files open in the editor contribute to context
3. **Cursor position** – Code immediately before and after the cursor
4. **Comments** – Natural language comments are the strongest signal
5. **Function signatures** – Method name + parameter types guide output

---

## 4.2 The Power of Strategic Comments

Comments are your primary communication channel with Copilot. Think of them as specifications.

### Pattern 1: Intent Comment Before a Test

```typescript
// Test: Verify that login fails when password is incorrect
// - Navigate to login page
// - Enter valid username but invalid password
// - Assert error message contains 'Invalid credentials'
test('login fails with wrong password', async ({ page }) => {
  // Copilot will complete the entire test body
```

### Pattern 2: Describe the Page Object Method

```typescript
// Returns all product names from the search results grid
// Waits for the grid to be visible before collecting
async getSearchResultNames(): Promise<string[]> {
  // Copilot completes with proper await and selector logic
```

### Pattern 3: Data Factory Comments

```typescript
// Generate a valid user payload with all required fields
// Email should be unique using timestamp
// Role should default to 'viewer'
function createUserPayload(overrides = {}) {
  // Copilot generates the object with realistic defaults
```

---

## 4.3 Tab-Accepting vs. Partial Acceptance

Copilot offers three levels of acceptance:

| Action | Result | When to use |
|---|---|---|
| `Tab` | Accept entire suggestion | When the whole completion is correct |
| `Ctrl+→` (word-by-word) | Accept one word at a time | When you want part of a suggestion |
| Type your own | Dismiss and retype | When suggestion is completely wrong |

**Pro tip:** Don't fight bad suggestions – just keep typing and the next suggestion will be better.

---

## 4.4 Test File Patterns That Accelerate Completions

### 4.4.1 Starting a Describe Block

```typescript
describe('Shopping Cart', () => {
  let cartPage: CartPage;

  beforeEach(async ({ page }) => {
    cartPage = new CartPage(page);
    await cartPage.navigate();
  });

  it('should add item to cart', // ← Tab here for full test body
  it('should update quantity',  // ← Tab here
  it('should remove item',      // ← Tab here
  it('should persist cart after refresh', // ← Tab here
```

### 4.4.2 Continuing Existing Patterns

When Copilot sees your first test, it pattern-matches subsequent ones perfectly:

```typescript
test('US phone number is valid', () => {
  expect(isValidPhone('+1-555-123-4567')).toBe(true);
});

test('UK phone number is valid', () => {  // ← start typing, Copilot continues the pattern
```

### 4.4.3 Data Provider Generation

```java
@DataProvider(name = "invalidEmails")
// Copilot generates the entire data matrix after this comment:
// - missing @ symbol
// - missing domain
// - missing TLD
// - spaces in email
// - consecutive dots
public Object[][] invalidEmailProvider() {
```

---

## 4.5 Page Object Method Completion

### TypeScript/Playwright

```typescript
export class CheckoutPage {
  readonly page: Page;
  
  constructor(page: Page) {
    this.page = page;
  }

  // Navigate to the checkout page
  async goto() {
    await this.page.goto('/checkout');
  }

  // Fill in shipping address form
  async fillShippingAddress(address: Address) {
    // Copilot fills: field filling with await, realistic selectors
  }

  // Select payment method by name
  async selectPaymentMethod(method: 'credit-card' | 'paypal' | 'apple-pay') {
    // Copilot generates the switch/match + interaction logic
  }
```

### Java/Selenium

```java
public class CheckoutPage extends BasePage {
    
    // Locators
    // Copilot generates @FindBy annotations after this comment:
    // - Email input field
    // - Password input field
    // - Submit button
    // - Error message container
    
    // Enter the shipping address into the form
    // @param address - the Address object with all fields
    public void fillShippingAddress(Address address) {
        // Copilot generates the sendKeys calls
    }
```

---

## 4.6 Next Edit Suggestions (NES)

Available in VS Code with `"github.copilot.nextEditSuggestions.enabled": true`

NES predicts WHERE your next edit will be (not just what to type). This is powerful for test automation when:
- You rename a locator – NES suggests all usages to update
- You add a parameter to a Page Object method – NES highlights all call sites
- You change an assertion type – NES suggests consistent updates

The suggestion appears as an arrow `→` in the gutter. Press `Tab` to jump to and accept the predicted edit.

---

## 4.7 Ghost Text Tips for API Testing

```python
# Python + pytest + requests example

class TestUserAPI:
    base_url = "https://api.example.com/v1"
    
    # Test creating a user with valid data returns 201
    def test_create_user_success(self, auth_headers):
        payload = {  # Copilot generates realistic payload
        
    # Test creating a user without required fields returns 422
    def test_create_user_missing_required_fields(self, auth_headers):
        # Copilot generates the incomplete payload + assertion pattern
        
    # Parametrize over multiple invalid email formats
    @pytest.mark.parametrize("invalid_email", [  # Copilot generates the list
```

---

## 4.8 Multi-Line Completion Strategies

When Copilot's suggestion is multi-line, use the **completions panel** (`Ctrl+Enter`) to see up to 10 alternatives. This is valuable when:
- Testing different assertion styles (chai vs jest vs should)
- Exploring different selector strategies (CSS vs xpath vs role-based)
- Comparing synchronous vs async implementations

---

## Knowledge Check

1. What is the strongest signal you can give Copilot to generate accurate test code?
2. How does "Next Edit Suggestions" differ from regular inline completions?
3. When would you use the Completions Panel instead of accepting the first suggestion?

---

## Exercises

**Exercise 4.1:** Open a new TypeScript file in VS Code. Write a comment describing a `ProductSearchPage` with five methods. Use Tab-accept to generate all method stubs in under 3 minutes.

**Exercise 4.2:** In IntelliJ, create a Java class with a comment describing a `UserApiClient` for REST calls. Observe how Copilot generates the entire RestTemplate/HttpClient setup.

**Exercise 4.3:** Rename a Page Object method and observe Next Edit Suggestions highlighting the call sites.

---

## Next Module

➡️ [Module 05: Copilot Chat Interface Deep Dive](../module-05/README.md)
