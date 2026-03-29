---
mode: agent
description: Generate comprehensive accessibility (a11y) tests using axe-core and Playwright. Tests WCAG 2.1 AA compliance for specified pages.
---

You are an accessibility testing specialist with deep knowledge of WCAG 2.1 and axe-core.

## Pages to Test (fill in below)

**Page(s) to test:**
- [Page name]: [URL or path]
- [Page name]: [URL or path]

**Accessibility Standard:** WCAG 2.1 Level AA (default - change if needed)

**Framework:** Playwright TypeScript with @axe-core/playwright

---

## Setup Requirements

### Install Dependencies

```bash
npm install --save-dev @axe-core/playwright axe-core
```

### Base Configuration

Create `src/helpers/AccessibilityHelper.ts`:

```typescript
import { Page, expect } from '@playwright/test';
import AxeBuilder from '@axe-core/playwright';

export class AccessibilityHelper {
  constructor(private page: Page) {}

  /**
   * Runs a full axe accessibility scan on the current page
   * Fails if any critical or serious violations found
   */
  async assertNoViolations(options?: { 
    disableRules?: string[],
    includedImpacts?: ('critical' | 'serious' | 'moderate' | 'minor')[]
  }): Promise<void> {
    const builder = new AxeBuilder({ page: this.page })
      .withTags(['wcag2a', 'wcag2aa', 'wcag21a', 'wcag21aa']);
    
    if (options?.disableRules) {
      builder.disableRules(options.disableRules);
    }
    
    const results = await builder.analyze();
    
    const violations = options?.includedImpacts 
      ? results.violations.filter(v => options.includedImpacts!.includes(v.impact as any))
      : results.violations.filter(v => ['critical', 'serious'].includes(v.impact!));
    
    if (violations.length > 0) {
      const report = violations.map(v => 
        `[${v.impact?.toUpperCase()}] ${v.id}: ${v.description}\n` +
        `  Help: ${v.helpUrl}\n` +
        `  Nodes: ${v.nodes.slice(0,3).map(n => n.html).join('\n         ')}`
      ).join('\n\n');
      
      throw new Error(`Accessibility violations found:\n\n${report}`);
    }
  }

  /**
   * Scans a specific section of the page
   */
  async assertSectionHasNoViolations(selector: string): Promise<void> {
    const results = await new AxeBuilder({ page: this.page })
      .include(selector)
      .withTags(['wcag2a', 'wcag2aa'])
      .analyze();
    expect(results.violations).toEqual([]);
  }
}
```

---

## Test Scenarios to Generate

### 1. Full Page Axe Scan

For each page provided:
```typescript
test('page has no critical/serious accessibility violations @a11y', async ({ page }) => {
  await page.goto('[URL]');
  const a11y = new AccessibilityHelper(page);
  await a11y.assertNoViolations();
});
```

### 2. Keyboard Navigation

```typescript
test('all interactive elements are keyboard accessible @a11y', async ({ page }) => {
  await page.goto('[URL]');
  
  // Tab through all focusable elements
  const focusableCount = await page.evaluate(() => {
    const focusable = document.querySelectorAll(
      'a, button, input, textarea, select, [tabindex]:not([tabindex="-1"])'
    );
    return focusable.length;
  });
  
  for (let i = 0; i < focusableCount; i++) {
    await page.keyboard.press('Tab');
    const focusedElement = await page.evaluate(() => document.activeElement?.tagName);
    expect(focusedElement).toBeTruthy();
  }
});

test('keyboard user can complete main action without mouse @a11y', async ({ page }) => {
  // Tab to the primary action button
  // Press Enter to activate
  // Assert the expected result
});
```

### 3. Images and Alt Text

```typescript
test('all images have descriptive alt text @a11y', async ({ page }) => {
  await page.goto('[URL]');
  
  const imagesWithoutAlt = await page.evaluate(() => {
    const images = Array.from(document.querySelectorAll('img'));
    return images
      .filter(img => !img.alt || img.alt.trim() === '')
      .map(img => img.src);
  });
  
  expect(imagesWithoutAlt, 
    `These images have no alt text: ${imagesWithoutAlt.join(', ')}`
  ).toHaveLength(0);
});
```

### 4. Form Labels

```typescript
test('all form inputs have associated labels @a11y', async ({ page }) => {
  await page.goto('[URL]');
  
  const unlabeledInputs = await page.evaluate(() => {
    const inputs = Array.from(document.querySelectorAll('input, textarea, select'));
    return inputs
      .filter(input => {
        const id = input.getAttribute('id');
        const ariaLabel = input.getAttribute('aria-label');
        const ariaLabelledBy = input.getAttribute('aria-labelledby');
        const label = id ? document.querySelector(`label[for="${id}"]`) : null;
        return !label && !ariaLabel && !ariaLabelledBy;
      })
      .map(input => input.outerHTML.substring(0, 100));
  });
  
  expect(unlabeledInputs, 
    `These inputs have no labels: ${unlabeledInputs.join('\n')}`
  ).toHaveLength(0);
});
```

### 5. Color Contrast (via axe)

```typescript
test('text meets WCAG AA color contrast requirements @a11y', async ({ page }) => {
  await page.goto('[URL]');
  
  const results = await new AxeBuilder({ page })
    .withRules(['color-contrast'])
    .analyze();
  
  expect(results.violations, 
    'Color contrast failures: ' + JSON.stringify(results.violations, null, 2)
  ).toHaveLength(0);
});
```

### 6. ARIA Roles and Landmarks

```typescript
test('page has correct landmark structure for screen readers @a11y', async ({ page }) => {
  await page.goto('[URL]');
  
  // Main landmark
  await expect(page.locator('main, [role="main"]')).toHaveCount(1);
  
  // Navigation landmark  
  await expect(page.locator('nav, [role="navigation"]')).toBeVisible();
  
  // Page has a title
  const title = await page.title();
  expect(title).toBeTruthy();
  expect(title.length).toBeGreaterThan(0);
});
```

### 7. Dynamic Content Announcements

```typescript
test('dynamic content changes are announced to screen readers @a11y', async ({ page }) => {
  await page.goto('[URL]');
  
  // Perform an action that creates dynamic content
  await page.getByRole('button', { name: '[action button name]' }).click();
  
  // Verify the live region announces the change
  await expect(page.locator('[aria-live]')).toBeVisible();
  await expect(page.locator('[aria-live]')).not.toBeEmpty();
});
```

---

## Output

Generate: `tests/accessibility/[feature]-a11y.spec.ts`

Also:
1. Update `playwright.config.ts` to include the a11y test path
2. Create a helper: `src/helpers/AccessibilityHelper.ts`
3. Add `@a11y` tag to all generated tests for easy filtering

Run command: `npx playwright test tests/accessibility/ --grep @a11y`

After generating, show:
- Total tests generated
- Which WCAG criteria each test covers
- Any pages where violations are expected (known issues)
