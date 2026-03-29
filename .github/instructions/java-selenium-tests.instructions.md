---
applyTo: "src/test/java/**/*.java"
---

# Java Selenium Test Instructions

These instructions apply to ALL Java test files in `src/test/java/`.

## Class Structure (TestNG)

```java
package com.example.automation.tests;

import com.example.automation.base.BaseTest;
import com.example.automation.pages.LoginPage;
import io.qameta.allure.*;
import org.testng.annotations.*;
import static org.assertj.core.api.Assertions.*;

@Epic("Authentication")
@Feature("Login")
public class LoginTests extends BaseTest {
    
    private LoginPage loginPage;

    @BeforeMethod
    public void setUp() {
        loginPage = new LoginPage(getDriver());
        loginPage.navigate("/");
    }

    @Test(groups = {"smoke", "regression"})
    @Story("Successful Login")
    @Description("Verify user can log in with valid credentials")
    @Severity(SeverityLevel.CRITICAL)
    public void shouldLoginWithValidCredentials() {
        // Arrange
        String username = TestConfig.STANDARD_USER;
        String password = TestConfig.STANDARD_PASSWORD;

        // Act
        loginPage.login(username, password);

        // Assert
        assertThat(getDriver().getCurrentUrl()).contains("inventory");
    }
}
```

## Naming Conventions

### Test Classes
Pattern: `[Feature]Tests`
```java
// ✅ Good
LoginTests, CheckoutTests, CartTests, UserApiTests

// ❌ Bad
LoginTest (singular), testLogin (verb form), Login_TC (underscore)
```

### Test Methods
Pattern: `should[Action][Context]` for TestNG
```java
// ✅ Good
shouldLoginWithValidCredentials()
shouldShowErrorWithLockedAccount()
shouldAddItemToCartSuccessfully()

// ❌ Bad
testLogin()          // redundant "test" prefix
loginTest()          // test suffix
TC001_login()        // number-based
```

## Required Annotations

Every `@Test` method must have:
1. `@Test(groups = {"smoke"})` or `@Test(groups = {"regression"})`
2. `@Story("...")` — the user story being tested
3. `@Severity(SeverityLevel.CRITICAL/NORMAL/MINOR)`

```java
@Test(groups = {"regression", "negative"})
@Story("Invalid Credentials Handling")
@Severity(SeverityLevel.NORMAL)
public void shouldShowErrorWithInvalidPassword() {
```

## Assertions

Use AssertJ exclusively. Never use TestNG's `Assert.*` or JUnit's assertions.

```java
// ✅ AssertJ
assertThat(actualText).isEqualTo("Expected text");
assertThat(items).hasSize(3);
assertThat(isVisible).isTrue();
assertThat(response.statusCode()).isEqualTo(200);

// ❌ TestNG Assert (do not use)
Assert.assertEquals(actualText, "Expected text");

// ❌ JUnit Assert (do not use)
assertEquals("Expected text", actualText);
```

## WebDriver Interactions

Always use Page Objects, never interact with WebDriver directly in tests:

```java
// ✅ Through Page Object
loginPage.login(username, password);
inventoryPage.addToCartByName("Sauce Labs Backpack");

// ❌ Direct WebDriver in test (belongs in Page Object)
driver.findElement(By.id("username")).sendKeys(username);
```

## Waits

Page Objects handle all waits. Tests should not contain wait logic.

```java
// ✅ Wait in Page Object (BasePage methods)
public void login(String username, String password) {
    sendKeys(usernameInput, username); // BasePage.sendKeys waits for visibility
    sendKeys(passwordInput, password);
    click(loginButton); // BasePage.click waits for clickability
}

// ❌ Wait in test (should not happen)
wait.until(ExpectedConditions.visibilityOf(element));
Thread.sleep(2000);
```

## Test Data

All test data must come from `TestDataProvider` or configuration:

```java
// ✅ From configuration
String username = TestConfig.STANDARD_USER;

// ✅ From DataProvider
@Test(dataProvider = "validCheckoutData", dataProviderClass = TestDataProvider.class)

// ❌ Hardcoded
String username = "standard_user"; // forbidden!
String password = "secret_sauce";  // forbidden!
```

## Forbidden

- ❌ `Thread.sleep()` — use WebDriverWait in BasePage
- ❌ Hardcoded credentials in test files
- ❌ `driver.findElement()` in test classes (only in Page Objects)
- ❌ `Assert.*` from TestNG — use AssertJ
- ❌ `@Test` methods without groups annotation
- ❌ Non-descriptive test method names (`test1()`, `loginTest()`)
