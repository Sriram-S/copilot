---
applyTo: "src/pages/**/*.ts"
---

# Playwright Page Object Instructions

These instructions apply to ALL Page Object files in `src/pages/`.

## Class Structure

```typescript
import { Page, Locator } from '@playwright/test';
import { BasePage } from './BasePage';

export class [Name]Page extends BasePage {
  // 1. All locators as readonly Locator properties
  readonly emailInput: Locator;
  readonly submitButton: Locator;
  readonly errorMessage: Locator;

  constructor(readonly page: Page) {
    super(page);
    // 2. Initialize all locators in constructor
    this.emailInput = page.getByLabel('Email address');
    this.submitButton = page.getByRole('button', { name: 'Submit' });
    this.errorMessage = page.getByRole('alert');
  }

  // 3. All methods are async
  async fillEmail(email: string): Promise<void> {
    await this.emailInput.fill(email);
  }

  async submit(): Promise<void> {
    await this.submitButton.click();
  }

  async getErrorMessage(): Promise<string> {
    return await this.errorMessage.innerText();
  }

  async isErrorVisible(): Promise<boolean> {
    return await this.errorMessage.isVisible();
  }
}
```

## Naming Conventions

### Locator Properties
Pattern: `[descriptor][ElementType]`
```typescript
// ✅ Good
readonly emailInput: Locator;
readonly submitButton: Locator;
readonly errorMessage: Locator;
readonly productCard: Locator;
readonly navigationMenu: Locator;

// ❌ Bad
readonly email: Locator;      // missing type suffix
readonly btn: Locator;        // abbreviated
readonly inputField1: Locator; // numbered
```

### Methods
Pattern: `[verb][Object]`
```typescript
// ✅ Good - verb + object
async fillEmail(email: string): Promise<void>
async selectPaymentMethod(method: PaymentMethod): Promise<void>
async clickAddToCart(): Promise<void>
async getProductName(): Promise<string>
async isLoggedIn(): Promise<boolean>

// ❌ Bad
async email(): Promise<void>        // missing verb
async doStuff(): Promise<void>      // vague
async setEmailField(): Promise<void> // "set" + "Field" is redundant
```

## Locator Strategy (Priority Order)

Always use the FIRST applicable strategy:

1. `page.getByRole()` — for buttons, links, headings, inputs with ARIA role
   ```typescript
   page.getByRole('button', { name: 'Add to Cart' })
   page.getByRole('link', { name: 'Products' })
   page.getByRole('heading', { name: 'Checkout' })
   ```

2. `page.getByLabel()` — for form fields with visible labels
   ```typescript
   page.getByLabel('Email address')
   page.getByLabel('Password')
   ```

3. `page.getByTestId()` — for elements with `data-testid` attribute
   ```typescript
   page.getByTestId('login-button')
   page.getByTestId('error-message')
   ```

4. `page.getByText()` — for unique, stable visible text
   ```typescript
   page.getByText('Welcome back')
   ```

5. `page.locator('css')` — LAST RESORT, add comment explaining why
   ```typescript
   // Using CSS because this button has no accessible name and no test ID
   page.locator('.checkout-btn')
   ```

## JSDoc Requirements

Every public method MUST have JSDoc:

```typescript
/**
 * Fills in the login form and submits it.
 * Waits for navigation to complete after successful login.
 * 
 * @param username - The user's email or username
 * @param password - The user's password
 * @throws TimeoutError if login page doesn't respond within 30s
 */
async login(username: string, password: string): Promise<void> {
  await this.usernameInput.fill(username);
  await this.passwordInput.fill(password);
  await this.submitButton.click();
}
```

## Forbidden Patterns

- ❌ `page.waitForTimeout()` — use Playwright's built-in waits
- ❌ `page.$('#selector')` — deprecated, use `page.locator()`
- ❌ Assertions in Page Objects (`expect()`) — assertions belong in tests
- ❌ `page.evaluate()` for things achievable with Locator API
- ❌ Returning raw DOM elements — return strings, booleans, or numbers
- ❌ `page.click('#selector')` — use `page.locator().click()` instead
