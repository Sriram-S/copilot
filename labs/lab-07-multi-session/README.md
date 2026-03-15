---
title: "Lab 07 — Parallel Agent Orchestra"
lab: 7
duration: "75 minutes"
tags: [multi-session, parallel-agents, handoff, session-management]
---

# Lab 07 — Parallel Agent Orchestra

## Objective

Run 3 parallel agent sessions simultaneously to build a multi-component feature
in a fraction of the time it would take sequentially.

---

## The Challenge: Build a User Notification System

You will build a notification system with 3 independent components. Each component
is assigned to a separate agent session.

```
┌──────────────────────────────────────────────────────────┐
│           User Notification System                        │
├────────────────┬──────────────────┬─────────────────────┤
│  Component A   │   Component B    │    Component C       │
│  Email Service │  SMS Service     │  Push Notification   │
│  (SMTP/SES)    │  (Twilio-style)  │  (FCM-style)         │
│                │                  │                      │
│ Session 1      │  Session 2       │  Session 3           │
└────────────────┴──────────────────┴─────────────────────┘
                           │
                    Session 4 (coordinator)
                 "Integrate and test all 3"
```

---

## Setup: File Partitioning

**Before starting**, define ownership (no overlaps):

| Session | Files Owned | Files Reads |
|---------|------------|-------------|
| Session 1 | `src/notifications/email.py`, `tests/notifications/test_email.py` | `src/notifications/base.py` |
| Session 2 | `src/notifications/sms.py`, `tests/notifications/test_sms.py` | `src/notifications/base.py` |
| Session 3 | `src/notifications/push.py`, `tests/notifications/test_push.py` | `src/notifications/base.py` |
| Session 4 | `src/notifications/dispatcher.py`, `tests/test_dispatcher.py` | All the above |

---

## Pre-work: Create the Base Class

Before starting the parallel sessions, create the shared base:

```python
# src/notifications/base.py
from abc import ABC, abstractmethod
from dataclasses import dataclass
from enum import Enum

class NotificationStatus(Enum):
    PENDING = "pending"
    SENT = "sent"
    FAILED = "failed"
    BOUNCED = "bounced"

@dataclass
class Notification:
    recipient_id: str
    subject: str
    body: str
    priority: str = "normal"  # "low", "normal", "high", "critical"

@dataclass
class NotificationResult:
    notification: Notification
    status: NotificationStatus
    provider_id: str | None = None
    error: str | None = None

class NotificationProvider(ABC):
    """Abstract base for all notification providers."""
    
    @abstractmethod
    async def send(self, notification: Notification) -> NotificationResult:
        """Send a notification. Returns result regardless of success/failure."""
        ...
    
    @abstractmethod
    async def health_check(self) -> bool:
        """Return True if the provider is available."""
        ...
```

---

## Session 1 Prompt: Email Service

```
CONTEXT: This is a Python notification system. The base class is at 
#file:src/notifications/base.py. Read it first.

GOAL: Implement EmailNotificationProvider that:
- Inherits from NotificationProvider
- Config: smtp_host, smtp_port, from_address, api_key (all from environment vars)
- send(): sends via SMTP using smtplib (simulate in tests, don't actually send)
- health_check(): verifies SMTP connection is reachable
- Handles: connection timeout, auth failure, invalid recipient
- File: src/notifications/email.py

THEN: Generate tests/notifications/test_email.py with:
- Test for successful send
- Test for SMTP timeout (mock socket)
- Test for auth failure
- Test for invalid recipient (bounce handling)
- Test for health_check success and failure
- Run tests: pytest tests/notifications/test_email.py -v

DEFINITION OF DONE: All tests pass, coverage ≥ 90%.
```

## Session 2 Prompt: SMS Service

```
CONTEXT: Same as Session 1 but for SMS.

GOAL: Implement SMSNotificationProvider that:
- Inherits from NotificationProvider
- Config: account_sid, auth_token, from_number (from env vars)
- send(): calls a mock Twilio-style REST API (use httpx, mock it in tests)
- health_check(): checks account balance > 0
- Handles: invalid phone number, insufficient balance, carrier error
- File: src/notifications/sms.py

THEN: Generate tests/notifications/test_sms.py
DEFINITION OF DONE: All tests pass, coverage ≥ 90%.
```

## Session 3 Prompt: Push Notification Service

```
CONTEXT: Same pattern but for push notifications.

GOAL: Implement PushNotificationProvider that:
- Inherits from NotificationProvider
- Config: fcm_api_key, project_id (from env vars)
- send(): calls a mock FCM-style REST API
- health_check(): validates API key
- Handles: invalid device token, rate limiting (429), service unavailable
- File: src/notifications/push.py

THEN: Generate tests/notifications/test_push.py
DEFINITION OF DONE: All tests pass, coverage ≥ 90%.
```

---

## After Parallel Sessions: Session 4 — Integration

Only start Session 4 when ALL of Sessions 1, 2, and 3 are complete:

```
CONTEXT: Three notification providers have been built:
- #file:src/notifications/email.py
- #file:src/notifications/sms.py  
- #file:src/notifications/push.py
- Base class: #file:src/notifications/base.py

GOAL: Build NotificationDispatcher in src/notifications/dispatcher.py:
- Accepts a list of providers at construction
- dispatch(notification, channels=["email","sms","push"]):
  - Sends via all specified channels concurrently (asyncio.gather)
  - Returns list of NotificationResult (one per channel)
  - Never raises — failures are captured in results
- retry(notification, channel, max_attempts=3):
  - Retries failed sends with exponential backoff
  - Returns final NotificationResult

THEN: Generate tests/test_dispatcher.py testing:
- Dispatch to single channel
- Dispatch to all channels concurrently
- Partial failure (one channel fails, others succeed)
- Retry logic: eventual success, all retries exhausted
- Verify async concurrency (all channels called simultaneously, not serially)

Run all tests: pytest tests/ -v
DEFINITION OF DONE: All tests pass across all 4 test files.
```

---

## Session State Handoff Exercise

After all sessions complete, practice the handoff ritual:

In the completed Session 1: 
```
Write a handoff summary to .copilot-session-notes/email-service.md:
- What was built
- How to run the tests
- Open issues or TODOs
- Integration notes for the dispatcher
```

Then verify the notes are useful to someone starting fresh.

---

## ✅ Completion Criteria

- [ ] All 3 provider tests pass in isolation
- [ ] All 3 providers run simultaneously without file conflicts
- [ ] Dispatcher implemented with async concurrency
- [ ] All dispatcher tests pass
- [ ] Combined: `pytest tests/ -v` shows 0 failures
- [ ] Session handoff notes written for at least 1 session
- [ ] Reflect: how much faster than sequential? Estimate the time saved.
