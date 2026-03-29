---
mode: agent
description: Generate comprehensive API tests for a REST endpoint. Provide the endpoint details and this prompt will generate tests for all standard scenarios.
---

You are a senior API test automation engineer.

Read `.github/copilot-instructions.md` first for project conventions.
Check `tests/api/` for existing API tests to match the exact style.

Generate comprehensive API tests for the following endpoint.

## Endpoint Details (fill in below)

**HTTP Method:** [GET / POST / PUT / PATCH / DELETE]
**URL:** [/api/resource or full URL]
**Description:** [What this endpoint does]

**Authentication:** [None / Bearer token / Basic Auth / API Key]
**Content-Type:** [application/json / multipart/form-data / none]

**Request Body (if applicable):**
```json
{
  "field1": "type - description",
  "field2": "type - description (required/optional)"
}
```

**Successful Response (2xx):**
```json
{
  "field1": "type",
  "field2": "type"
}
```
**Status Code:** [200 / 201 / 204]

**Error Responses:**
- 400: [when this happens]
- 401: [auth required]
- 403: [insufficient permissions]
- 404: [resource not found]
- 422: [validation failure]

## Generation Requirements

### Framework
- TypeScript + Jest + supertest (or axios if supertest not available)
- Check existing tests and match their import/setup pattern

### Test Organization
```
describe('[Method] [URL]', () => {
  describe('Success cases', () => { ... })
  describe('Validation errors', () => { ... })
  describe('Authentication', () => { ... })
  describe('Not found cases', () => { ... })
})
```

### Required Test Scenarios

#### 1. Happy Path Tests
- Valid request with ALL required + optional fields
- Valid request with ONLY required fields
- Verify: exact status code, response body schema, field types

#### 2. Required Field Validation
For EACH required field, test:
- Missing the field entirely → 400/422 with error message mentioning that field
- Empty string/null for string fields → 400/422
- Wrong type (string where number expected) → 400/422

#### 3. Format Validation (where applicable)
- Invalid email format → 400/422
- Invalid date format → 400/422
- Value out of range (negative where positive required) → 400/422
- String longer than max length → 400/422

#### 4. Authentication Tests
- No auth header → 401
- Invalid/malformed token → 401
- Expired token → 401
- Valid token but wrong permissions → 403

#### 5. Not Found Tests (for endpoints with resource IDs)
- Valid format but non-existent ID → 404
- Deleted resource ID → 404
- ID from different user → 404 (or 403)

#### 6. Response Schema Validation
Every 2xx response must be validated:
```typescript
// Validate all required fields present
expect(response.body).toHaveProperty('id');
expect(typeof response.body.id).toBe('number');
// etc. for each field
```

#### 7. Response Time
```typescript
const start = Date.now();
const response = await request(...);
const duration = Date.now() - start;
expect(duration).toBeLessThan(2000); // 2 second SLA
```

### Code Quality Requirements
- Use `beforeAll` for auth setup (get token once)
- Use `afterAll` to clean up created resources (DELETE by ID)
- Use test data factory or generate unique values (avoid hardcoded IDs)
- No hardcoded credentials — use environment variables
- Every `describe` block has a clear name explaining the scenario group

## Output

File: `tests/api/[resource-name].api.spec.ts`

After creating, show:
- Total test count
- Breakdown by category (happy path / validation / auth / etc.)
- Any scenarios that couldn't be tested (and why)
- Command to run just these tests
