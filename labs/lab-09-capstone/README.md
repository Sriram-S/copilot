---
title: "Lab 09 — Capstone: End-to-End Automation System"
lab: 9
duration: "3-4 hours (or split across 2 sessions)"
tags: [capstone, end-to-end, real-world, full-stack, all-features]
---

# Lab 09 — Capstone: End-to-End Automation System

## Objective

Build a complete, real-world automation system for **BookingAPI** — a hotel room booking
service — using every technique from this course. This is the proof that you have reached
the top 1%.

**Rule**: Copilot writes the code. You are the architect.

---

## BookingAPI Specification

```
Service: Hotel Room Booking API

Entities:
- Hotel: id, name, city, star_rating, amenities[]
- Room: id, hotel_id, type, capacity, price_per_night, is_available
- Booking: id, room_id, guest_name, guest_email, check_in, check_out, status, total_price
- Guest: id, email, name, bookings[]

Endpoints:
- GET    /hotels?city=&min_stars=                 # Search hotels
- GET    /hotels/{id}/rooms?check_in=&check_out=  # Available rooms
- POST   /bookings                               # Create booking
- GET    /bookings/{id}                          # Get booking
- PATCH  /bookings/{id}/cancel                   # Cancel booking
- GET    /guests/{id}/bookings                   # Guest booking history

Business Rules:
- Rooms cannot be double-booked
- Cancellations > 48h before check-in: full refund
- Cancellations < 48h: 50% penalty
- check_out must be after check_in
- Minimum stay: 1 night
```

---

## Phase 1: Framework Setup (30 min, 2 parallel sessions)

### Session A: Python Framework + copilot-instructions.md

```
GOAL: Bootstrap a Python pytest automation framework for BookingAPI.

1. Create pyproject.toml with: pytest, httpx, playwright, allure-pytest,
   factory_boy, faker, freezegun, respx, pytest-asyncio, ruff, mypy
   
2. Create directory structure:
   tests/unit/, tests/integration/, tests/e2e/, tests/performance/
   src/api_client/, src/pages/, src/data_factory/
   
3. Create conftest.py with fixtures:
   - async_client (httpx.AsyncClient against mock)
   - booking_api_client (BookingAPIClient instance)
   - auth_headers (Bearer token header dict)
   
4. Create .github/copilot-instructions.md for this project capturing
   all the standards you've learned in this course.
```

### Session B: Data Factories

```
GOAL: Create factory_boy factories for all 4 entities.
Reference: #file:pyproject.toml for the stack.

Create src/data_factory/factories.py with:
- HotelFactory (realistic city names, star ratings 1-5, realistic amenities)
- RoomFactory (hotel_id from HotelFactory, realistic room types, valid prices)
- BookingFactory (valid date ranges, check_out always after check_in)
- GuestFactory (faker email, name, linked bookings)

Each factory must have _build and _create strategies.
Use Faker with locale=en_GB.
```

---

## Phase 2: API Client (20 min)

```
GOAL: Generate the BookingAPIClient following docstring-first development.

src/api_client/booking_client.py

"""
Async HTTP client for BookingAPI.
Methods:
- search_hotels(city, min_stars) → list[dict]
- get_available_rooms(hotel_id, check_in, check_out) → list[dict]
- create_booking(room_id, guest_email, check_in, check_out) → dict
- get_booking(booking_id) → dict
- cancel_booking(booking_id) → dict
- get_guest_bookings(guest_id) → list[dict]

All async. All raise BookingAPIError on non-2xx.
"""

THEN: Generate unit tests for each method using respx for HTTP mocking.
Target: 100% method coverage.
```

---

## Phase 3: Integration Tests (45 min, 3 parallel sessions)

### Session 1: Hotel & Room Search Tests

```
Generate integration tests for:
- Hotel search: by city, by stars, combined filters, no results
- Available rooms: valid dates, invalid dates (check_out before check_in),
  room already booked (unavailable), all rooms available
- Parametrize: test multiple cities and star ratings in one test
```

### Session 2: Booking Lifecycle Tests

```
Generate integration tests for the full booking lifecycle:
- Happy path: search → select room → book → confirm → check booking details
- Double booking prevention (409 Conflict)
- Cancellation > 48h (full refund verified)
- Cancellation < 48h (50% penalty verified)
- Cancel non-existent booking (404)
- Get booking history for a guest (empty, single, multiple)
```

### Session 3: Validation & Edge Case Tests

```
Generate validation tests:
- check_in = check_out (same day) → 422
- check_in in the past → 422
- check_out before check_in → 422
- Missing required fields → 422 with field-level errors
- Invalid email format → 422
- Non-existent room_id → 404
- Non-existent hotel_id → 404
- Boundary: 1 night stay (minimum valid)
- Boundary: 365 night stay (maximum valid, if any)
```

---

## Phase 4: E2E Tests with Playwright (45 min)

```
Generate Playwright page objects and E2E tests for BookingAPI's web frontend.

Page Objects:
- SearchPage: city input, star filter, search button, results list
- HotelDetailPage: room list, date picker, "Book Now" button
- BookingFormPage: guest details form, payment section, confirm button
- BookingConfirmationPage: booking reference, summary, "Cancel Booking" button

E2E Scenarios (use pytest-bdd Gherkin format):
- Happy path booking
- Search with no results
- Attempt to book unavailable room (error handling)
- Cancel booking from confirmation page
```

---

## Phase 5: CI/CD Pipeline (20 min)

```
Run: /generate-ci-pipeline

Additional requirements:
- Matrix: Python 3.11 and 3.12
- Integration tests require a mock server (use pytest-httpserver)
- E2E tests install Playwright browsers automatically
- Allure report published to GitHub Pages
- Performance test (k6) runs weekly via schedule, not on every PR
- Fail the pipeline if integration test coverage drops below 80%
```

---

## Phase 6: Delivery Report (15 min, Agent Mode)

```
Generate DELIVERY-REPORT.md for the BookingAPI Automation Framework.
Include:
- Executive summary
- Architecture diagram (Mermaid)
- Test pyramid statistics (unit/integration/e2e counts and coverage)
- CI/CD pipeline diagram (Mermaid)
- How to run tests locally
- How to extend with a new endpoint
- Open issues and future improvements

Format: GitHub-flavored Markdown, ready to share with stakeholders.
```

---

## Final Checklist

### Code
- [ ] `pytest tests/ -v` — 0 failures, ≥ 60 total tests
- [ ] Coverage ≥ 85% on API client
- [ ] At least 1 Playwright E2E test passing
- [ ] CI YAML valid and syntactically correct

### Copilot Configuration
- [ ] `.github/copilot-instructions.md` — comprehensive and specific
- [ ] At least 2 `.instructions.md` scoped files
- [ ] At least 2 `.prompt.md` files (can reuse from `.github/prompts/`)
- [ ] At least 1 custom agent in `.github/agents/`

### Documentation
- [ ] `DELIVERY-REPORT.md` complete with diagrams
- [ ] `README.md` with "How to run" section
- [ ] At least 1 session handoff note from multi-session work

---

## Scoring Rubric (Self-Assessment)

| Criterion | Max Points | Your Score |
|-----------|-----------|-----------|
| Test count and variety (unit + integration + E2E) | 30 | |
| Coverage ≥ 85% | 20 | |
| CI pipeline completeness | 15 | |
| Copilot config quality (instructions, prompts, agents) | 20 | |
| Delivery report clarity | 15 | |
| **Total** | **100** | |

**Top 1% threshold: 85+**

---

## 🏆 You've Made It

If you've reached this point with a working, tested, documented automation framework
built predominantly by Copilot under your architectural guidance — you have achieved
what this course set out to deliver.

The techniques, configurations, and patterns in your `.github/` directory are now
**your team's unfair advantage**. Use them, share them, and keep evolving them.

*"The best automation engineer is not the one who writes the most code —
it's the one who ships the highest quality with the greatest leverage."*
