---
title: "Module 01 — Copilot Fundamentals"
module: 1
tags: [fundamentals, mental-model, context, completions]
---

# Module 01 — Copilot Fundamentals

## Learning Objectives

- Build the correct mental model of how Copilot generates suggestions
- Understand what "context" means and how to maximise it
- Know the difference between all Copilot interaction modes
- Develop habits that consistently produce high-quality completions

---

## 1.1 The Mental Model — How Copilot Actually Works

Copilot is a **large language model (LLM)** trained on billions of lines of public code. It does
not search a database — it predicts what code tokens are statistically most likely to follow the
tokens it sees in your context window.

**What this means for you:**

| Principle | Implication |
|-----------|-------------|
| Copilot sees a sliding window of your file and open tabs | More relevant context = better suggestions |
| It has no memory between sessions (unless using agents) | Re-establish context at the start of each session |
| It follows patterns more than instructions | Show an example and it will continue the pattern |
| Specificity improves output | Vague prompts → vague code |

---

## 1.2 The Context Window — What Copilot Sees

Copilot uses content from (in priority order):

1. **Your current file** (above and below the cursor)
2. **Open tabs** in the editor
3. **`.github/copilot-instructions.md`** (always injected)
4. **Relevant `.instructions.md` files** (matched by `applyTo` glob)
5. **Files you explicitly reference** with `#file:path` in Chat
6. **@workspace** search results (when you use `@workspace`)

### Practical Implication

**Do this before starting a session:**

```
1. Open the files you'll be working with
2. Open related test files in adjacent tabs
3. Open any API/interface definition files
```

---

## 1.3 Interaction Modes

### Mode 1: Inline Completions (Ghost Text)

- Triggered automatically as you type
- Press `Tab` to accept, `Esc` to dismiss
- Press `Ctrl+Enter` to see all suggestions
- Best for: boilerplate, repetitive patterns, standard algorithms

### Mode 2: Inline Chat

- Trigger: select code → `Ctrl+I` / `Cmd+I`
- Ask Copilot to transform selected code
- Accepts `/fix`, `/explain`, `/doc` slash commands
- Best for: targeted refactoring, explaining complex logic, adding docs

### Mode 3: Chat Panel

- Trigger: `Ctrl+Alt+I` / `Ctrl+Cmd+I`
- Persistent conversation with context
- Supports `@workspace`, `@vscode`, `#file:`, `#symbol:` references
- Best for: architectural questions, multi-file exploration

### Mode 4: Agent Mode

- Switch the Chat panel model to "Agent"
- Copilot can read files, run commands, iterate on errors
- Best for: implementing features, writing + running tests, building pipelines

---

## 1.4 Context Priming Techniques

### Technique 1: Docstring-First Development

Write the docstring describing what you want **before** the code:

```python
def calculate_discount(
    original_price: float,
    customer_tier: str,
    coupon_code: str | None = None
) -> float:
    """
    Calculate the final price after applying tier-based discount and optional coupon.

    Tier discounts: standard=0%, silver=10%, gold=20%, platinum=30%
    Coupon codes override tier discounts if the coupon discount is higher.
    Prices cannot go below 0.

    Args:
        original_price: Price before any discounts, must be >= 0
        customer_tier: One of 'standard', 'silver', 'gold', 'platinum'
        coupon_code: Optional coupon code; invalid codes are silently ignored

    Returns:
        Final price after applying the highest applicable discount

    Raises:
        ValueError: If original_price is negative
    """
    # Copilot will now generate a complete, correct implementation
```

### Technique 2: Example-Continuation

Show one example, let Copilot continue the pattern:

```python
# API endpoint tests follow this pattern:
# 1. Arrange: set up mocks and test data
# 2. Act: call the endpoint
# 3. Assert: verify response code and body

def test_get_user_returns_200_for_existing_user():
    # Arrange
    mock_repo.get_user.return_value = User(id=1, name="Alice")
    # Act
    response = client.get("/users/1")
    # Assert
    assert response.status_code == 200
    assert response.json()["name"] == "Alice"

def test_get_user_returns_404_for_missing_user():
    # Copilot continues the pattern here...
```

### Technique 3: Comment-Driven Implementation

Use comments as a step-by-step specification:

```python
def process_payment(order_id: str, payment_method: PaymentMethod) -> PaymentResult:
    # 1. Fetch the order from the database
    # 2. Validate the order is in PENDING state
    # 3. Calculate the total including taxes and shipping
    # 4. Charge the payment method via the payment gateway
    # 5. If charge succeeds: update order status to PAID, send confirmation email
    # 6. If charge fails: update order status to PAYMENT_FAILED, log error
    # 7. Return PaymentResult with status and transaction ID
```

---

## 1.5 Anatomy of a Good vs. Bad Prompt

### ❌ Bad (vague, no context)
```python
# function for users
```

### ✅ Good (specific, typed, documented intent)
```python
# Parse a JWT token, validate its signature using the RS256 algorithm,
# check expiry, and return the decoded claims dict.
# Raise AuthError if the signature is invalid or the token is expired.
# Use the PyJWT library.
def validate_jwt(token: str, public_key: str) -> dict[str, Any]:
```

---

## 1.6 The 5 Laws of Copilot Quality

1. **Law of Specificity** — the more specific your prompt, the better the output
2. **Law of Context** — open related files before asking; quality scales with context
3. **Law of Iteration** — accept partial suggestions, then refine; rarely get perfection first try
4. **Law of Review** — always read generated code; Copilot is a brilliant intern, not an oracle
5. **Law of Investment** — time spent on `.instructions.md` pays compound interest

---

## Module Checkpoint

- [ ] Understand the context window and what Copilot sees
- [ ] Know all 4 interaction modes and when to use each
- [ ] Practised docstring-first development on 2+ functions
- [ ] Practised example-continuation on a test suite
- [ ] Written your first inline comment-driven implementation

**Next**: [Module 02 — Inline Completions Mastery](./MODULE-02-inline-completions.md)
