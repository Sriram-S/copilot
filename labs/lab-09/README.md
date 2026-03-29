# Lab 09: Capstone — Java Selenium Suite with IntelliJ + Copilot

## Objective
Build a complete Java Selenium TestNG test framework using GitHub Copilot in IntelliJ IDEA, demonstrating the full IntelliJ Copilot experience for Java automation engineers.

## Duration
90 minutes

## Prerequisites
- IntelliJ IDEA 2024.1+ with GitHub Copilot plugin installed
- Java 17+
- Maven 3.8+
- Chrome browser installed

---

## The Scenario

You are a Java QA Engineer. Your team uses Selenium 4 + TestNG + Maven + Allure. Build a complete test framework for SauceDemo from scratch using Copilot inside IntelliJ.

---

## Phase 1: Project Setup (10 min)

### Step 1: Create a Maven Project

In IntelliJ:
1. **File → New Project → Maven Archetype**
2. Use `maven-archetype-quickstart`
3. GroupId: `com.example.automation`
4. ArtifactId: `saucedemo-tests`

### Step 2: Generate pom.xml with Copilot

Place cursor inside `pom.xml` and use Copilot Chat:

```
Update this Maven pom.xml to include all dependencies for a Selenium 4 + TestNG 
test automation framework.

Include:
- selenium-java 4.20.0
- testng 7.10.1
- webdrivermanager 5.8.0 (for driver management)
- allure-testng 2.27.0 (for reporting)
- allure-java-commons 2.27.0
- assertj-core 3.25.3
- lombok 1.18.32 (for @Data, @Builder annotations)
- slf4j-simple 2.0.13 (for logging)
- maven-surefire-plugin 3.2.5 configured for TestNG
- aspectjweaver (for Allure)
- java 17 compiler

Also add properties:
- selenium.version
- testng.version
- allure.version
Configure maven-surefire to run testng.xml

Output the complete updated pom.xml
```

---

## Phase 2: Framework Architecture (25 min)

### Step 3: Generate DriverManager

In Copilot Chat (IntelliJ):

```
Generate a ThreadLocal-based WebDriverManager class for Selenium 4.

Requirements:
- Supports Chrome, Firefox, Edge (configured via system property "browser")
- Thread-safe using ThreadLocal<WebDriver>
- Method: initDriver() - initializes with WebDriverManager
- Method: getDriver() - returns current thread's driver
- Method: quitDriver() - quits and removes from ThreadLocal
- Chrome options: headless mode when CI=true env var is set
- Implicit wait: 0 (we use explicit waits)
- Window maximize on initialization

Output: src/main/java/com/example/automation/driver/DriverManager.java
```

### Step 4: Generate BasePage

```
Generate a BasePage class for Selenium 4 Page Objects.

Requirements:
- Constructor takes WebDriver
- Uses WebDriverWait (10 second timeout)
- Has protected helper methods:
  - click(WebElement element) - with explicit wait for clickability
  - sendKeys(WebElement element, String text) - clears first, then types
  - getText(WebElement element) - with explicit wait for visibility
  - isVisible(WebElement element) - returns boolean
  - waitForElementVisible(By locator) - explicit wait
  - scrollToElement(WebElement element) - uses JavascriptExecutor
  - selectByVisibleText(WebElement select, String text) - Select class
- Uses PageFactory.initElements in constructor
- Uses @FindBy annotations (not driver.findElement)
- Allure step annotation on helper methods

Output: src/main/java/com/example/automation/pages/BasePage.java
```

### Step 5: Generate BaseTest

```
Generate a BaseTest class for TestNG.

Requirements:
- @BeforeMethod: initializes WebDriver via DriverManager
- @AfterMethod: quits driver via DriverManager, captures screenshot on failure
- Screenshot method: captures and attaches to Allure report
- Uses ITestResult parameter in @AfterMethod to detect failures
- Logs test start/end with SLF4J
- Gets base URL from system property "baseUrl" with default "https://www.saucedemo.com"

Output: src/test/java/com/example/automation/base/BaseTest.java
```

### Step 6: Generate Page Objects

Use Alt+Enter in IntelliJ or Chat:

```
Generate three Selenium Page Object classes for SauceDemo.

1. LoginPage.java
   URL: https://www.saucedemo.com/
   Elements (use @FindBy with data-test attributes):
   - usernameInput: data-test="username"
   - passwordInput: data-test="password"  
   - loginButton: data-test="login-btn"
   - errorMessage: data-test="error"
   Methods:
   - login(String username, String password)
   - getErrorMessage(): String
   - isLoginButtonVisible(): boolean

2. InventoryPage.java
   URL: /inventory.html
   Elements:
   - productNames: data-test="inventory-item-name" (List<WebElement>)
   - addToCartButtons: data-test contains "add-to-cart" (List<WebElement>)
   - sortDropdown: data-test="product_sort_container"
   - cartBadge: class="shopping_cart_badge"
   Methods:
   - getProductNames(): List<String>
   - addToCartByName(String productName)
   - sortBy(String option)
   - getCartItemCount(): int

3. CartPage.java
   URL: /cart.html  
   Elements:
   - cartItems: class="cart_item" (List<WebElement>)
   - checkoutButton: data-test="checkout"
   - removeButtons: data-test contains "remove" (List<WebElement>)
   Methods:
   - getCartItemCount(): int
   - getCartItemNames(): List<String>
   - removeItemByName(String productName)
   - proceedToCheckout()

All extend BasePage. Use Allure @Step on all methods.
Output: src/main/java/com/example/automation/pages/[ClassName].java
```

---

## Phase 3: Test Generation (25 min)

### Step 7: Generate Test Data

```
Generate a TestDataProvider class for SauceDemo tests.

Include:
@DataProvider for authentication scenarios:
- validUser: standard_user / secret_sauce
- lockedUser: locked_out_user / secret_sauce
- problemUser: problem_user / secret_sauce
- invalidCredentials: random@invalid.com / wrongpassword

@DataProvider for checkout info:
- validCheckoutInfo: { firstName, lastName, zipCode } (3 different datasets)
- invalidCheckoutInfo: missing firstName, missing lastName, invalid zip

Use Lombok @Data and @Builder for the data classes.
Output: src/test/java/com/example/automation/data/TestDataProvider.java
```

### Step 8: Generate Tests

Place cursor inside a test class and use `Alt+Enter → "Generate Tests with Copilot"` OR use Chat:

```
Generate comprehensive TestNG test classes for SauceDemo.

Use:
- BaseTest as parent class
- TestDataProvider for test data
- All Page Object classes created above
- AssertJ for assertions (assertThat())
- Allure @Epic, @Feature, @Story annotations

Generate:

1. AuthenticationTests.java
   - loginWithValidCredentials() - @Story("Successful Login")
   - loginWithLockedUser_ShowsError()
   - loginWithInvalidCredentials_ShowsError()
   - loginWithEmptyUsername_ShowsError()
   - loginWithEmptyPassword_ShowsError()
   - Data-driven: loginScenarios() using @DataProvider

2. InventoryTests.java
   - productsAreDisplayedAfterLogin()
   - sortProductsByNameAZ()
   - sortProductsByPriceLowToHigh()
   - addSingleProductToCart_UpdatesBadge()
   - addMultipleProductsToCart()

3. CartTests.java
   - addedProductsAppearInCart()
   - removeProductFromCart()
   - cartCountUpdatesAfterRemoval()
   - proceedToCheckoutFromCart()

Output: src/test/java/com/example/automation/tests/[ClassName].java
```

---

## Phase 4: Configuration & CI (15 min)

### Step 9: Generate TestNG XML

```
Generate a testng.xml suite configuration.

Suite name: "SauceDemo Automation Suite"
Groups: smoke (run on PR), regression (run on push to main)

Include:
- Authentication tests (all groups)
- Inventory tests (smoke: products display; regression: all)
- Cart tests (smoke: add to cart; regression: all)

Configure:
- parallel="methods" thread-count="4"
- listeners: Allure TestNG listener

Output: src/test/resources/testng.xml
```

### Step 10: Generate GitHub Actions for Maven

```
Generate a GitHub Actions workflow for our Maven Selenium TestNG project.

Requirements:
- Trigger: push to main, pull_request
- OS: ubuntu-latest
- Java: 17 (temurin)
- Chrome: setup-chrome action for latest stable
- Run: mvn test -Dheadless=true -DbaseUrl=${{ secrets.STAGING_URL }}
- Allure report: generate and publish to GitHub Pages
- Screenshots: upload on failure
- Fail fast: false (complete all tests even if some fail)
- Test summary: post results as PR comment

Output: .github/workflows/selenium-tests.yml
```

---

## Phase 5: IntelliJ-Specific Features (15 min)

### Step 11: Copilot Code Actions in IntelliJ

Practice these IntelliJ-specific Copilot features:

**Alt+Enter on a Test Method:**
1. Open `AuthenticationTests.java`
2. Place cursor on `loginWithValidCredentials()` method
3. Press `Alt+Enter`
4. Select "Explain with Copilot" - read the explanation
5. Press `Alt+Enter` again
6. Select "Generate Javadoc with Copilot"

**Inline Chat in IntelliJ:**
1. Select the `sortProductsByNameAZ()` test method
2. Right-click → Copilot → "Ask Copilot"
3. Type: "Add a data-driven version of this test that also tests ZA and Price High-Low sorting"

**Commit Message Generation:**
1. Stage your files in Git (VCS → Commit)
2. Click the Copilot sparkle icon (✨) in the commit dialog
3. Accept or refine the generated commit message

### Step 12: Refactoring with Copilot

```
Refactor AuthenticationTests.java to:
1. Extract all common setup into a @BeforeClass that creates the LoginPage once
2. Use @DataProvider for ALL login scenarios (currently some are separate tests)
3. Add @Severity(SeverityLevel.CRITICAL) to all @Test annotations
4. Add meaningful @Description annotations to each test

Show the complete refactored class.
```

---

## Phase 6: Run and Verify

```bash
# Run smoke tests
mvn test -Dgroups=smoke -Dheadless=true

# Generate Allure report
mvn allure:serve
```

If failures, use Copilot Chat with the Maven output pasted.

---

## Retrospective

1. How did IntelliJ's `Alt+Enter → Generate Tests` compare to VS Code's `/tests` command?
2. Which Copilot features are IntelliJ-specific and which are shared with VS Code?
3. What adjustments would you make to the instructions file for a Java/Maven project?

---

## Expected Outcome

- [ ] Maven project with complete pom.xml
- [ ] Framework: DriverManager, BasePage, BaseTest
- [ ] 3 Page Objects: LoginPage, InventoryPage, CartPage
- [ ] 3 Test classes with 15+ tests total
- [ ] TestNG XML configuration
- [ ] GitHub Actions workflow
- [ ] Smoke tests running (headless Chrome)
- [ ] Allure report generated

---

## 🎓 Course Complete!

Congratulations! You have completed the **Copilot Automation Mastery Course**.

You can now:
- Scaffold complete test frameworks with Agent mode
- Generate tests from source code, specs, and requirements
- Debug flaky tests with AI assistance
- Build custom agents and prompts for your team
- Configure Copilot with workspace instructions for consistent output
- Integrate Copilot into your daily QA workflow

**Return to:** [Course Overview](../../README.md)
