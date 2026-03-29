---
mode: agent
description: Generate a complete Playwright TypeScript Page Object for a given page. Provide the page name, URL, and list of user actions.
---

You are an expert Playwright automation engineer.

Read `.github/copilot-instructions.md` first for project conventions.
Then read `src/pages/BasePage.ts` to understand the base class structure.
Then check existing page objects in `src/pages/` to match the code style exactly.

Generate a TypeScript Page Object class for the following page.

## Page Information (fill in below)

**Page Name:** [PAGE_NAME]
**URL Path:** [URL_PATH or full URL]
**Description:** [What the user does on this page]

**User Actions Available on This Page:**
1. [Action 1]
2. [Action 2]
3. [Action 3]
(add more as needed)

**Key UI Elements:**
- [element description]: [selector hint if known]
(add more as needed)

## Generation Requirements

### Class Structure
- Class name: `[PageName]Page`
- File location: `src/pages/[PageName]Page.ts`
- Must extend `BasePage`
- Export as named export (not default)

### Locator Strategy (priority order):
1. `getByRole()` — for buttons, links, headings, inputs with ARIA roles
2. `getByLabel()` — for form fields with visible labels
3. `getByTestId()` — for elements with `data-testid` attribute
4. `getByText()` — for unique, stable visible text
5. `locator('css')` — ONLY as last resort, with a comment explaining why

### Locator Naming
- Pattern: `[descriptor][ElementType]`
- Examples: `submitButton`, `emailInput`, `errorMessage`, `productCard`
- Prefix with `readonly` and type as `Locator`
- Group all locators at the top of the class

### Method Requirements
- ALL methods must be `async`
- Return `Promise<void>` unless returning a value
- Return `Promise<string>` for text/value getters
- Return `Promise<boolean>` for state checks
- Return `Promise<string[]>` for lists
- Method naming: `[verb][Object]` — e.g., `fillEmail()`, `selectPaymentMethod()`, `isErrorVisible()`
- Every public method needs JSDoc with `@param` and `@returns`

### Forbidden Patterns
- ❌ `page.waitForTimeout()` — use Playwright's built-in auto-waiting
- ❌ String selectors directly in tests — selectors live ONLY in Page Objects
- ❌ `async (page) =>` in page object methods — `this.page` is already available

## After Generating

1. Create the file at `src/pages/[PageName]Page.ts`
2. Show a summary of:
   - Number of locators defined
   - Number of methods created
   - Any assumptions made about selectors (flagged for human review)
3. Suggest: "Use this Page Object with: `#file:src/pages/[PageName]Page.ts` when generating tests"
